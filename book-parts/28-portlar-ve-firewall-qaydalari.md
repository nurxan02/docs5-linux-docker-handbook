# Bölmə 28. Portlar və firewall qaydaları (hansı server → hansı server → hansı port)

Bu bölmə Docs5 arxitekturasında **şəbəkə icazələrini (firewall rules)** düzgün planlamaq üçündür: hansı komponent hansı komponentə hansı portdan çıxmalıdır.

> Vacib qeyd (dəqiqlik): Bu workspace-də `Sources/` altında gördüyümüz `docker-compose.yml` faylları əsasən **utility/migration** konteynerləridir və “tam prod stack” (nginx, docs web/api, keycloak, storage və s.) compose-u burada yoxdur. Ona görə aşağıdakı port cədvəli **Docs5 üçün tipik/baza port matrisi** kimi verilir və sonda mühitinizdən **real port siyahısını 5 dəqiqəyə çıxarma** addımları əlavə olunur.

## 28.1 Port anlayışı: inbound vs outbound

- **Inbound**: digər serverdən bu serverə gələn trafik (firewall-da “allow incoming”).
- **Outbound**: bu serverin başqa serverə getməsi (çox mühitdə outbound açıq olur, amma korporativ şəbəkələrdə outbound da məhdud ola bilər).

Əsas prinsip:

- “Hamıya açıq” yox, **yalnız lazım olan mənbədən** (source IP/subnet) **yalnız lazım olan porta** icazə.

## 28.2 Tipik Docs5 komponentləri və portları

Aşağıdakı portlar Docs5 tipli arxitekturalarda ən çox rast gəlinən standartlardır:

- Reverse proxy (Nginx): `80/tcp`, `443/tcp`
- PostgreSQL: `5432/tcp`
- Keycloak (HTTP): `8080/tcp` (bəzi qurulumlarda)
- Keycloak (HTTPS): `8443/tcp` (bəzi qurulumlarda)
- Vault: `8200/tcp`
- Storage (S3-compatible): çox vaxt `9000/tcp` (MinIO default), bəzən `443/tcp` (əgər storage front-da HTTPS-dirsə)
- Docs5 Web/API (Kestrel və ya app server): çox vaxt `5000/tcp`, `5001/tcp` (mühitə görə dəyişir)

> Sizdə fərqli ola bilər. Dəqiq portlar üçün 28.6 bölməsindəki “real çıxartma” addımlarını edin.

## 28.3 Arxitektura variantları (port ehtiyacı buna görə dəyişir)

### Variant A: “Hər şey 1 serverdə” (ən sadə)

- Internet/LB → Nginx server: `80/443`
- Nginx → Docs5 app: **localhost** (host daxilində) → firewall lazım olmur
- App → PostgreSQL: **localhost** → firewall lazım olmur
- App → Keycloak/Vault/Storage: yenə localhost və ya eyni host network

Bu variantda firewall ən çox:

- `22/tcp` (SSH) yalnız jump/office IP-dən
- `80/443` internet/LB-dən

açmaqla kifayətlənir.

### Variant B: “Servislər ayrı serverlərdə” (ən real prod)

Bu halda firewall rules vacib olur:

- Nginx server → App server (internal port)
- App server → DB server `5432`
- App server → Keycloak `8080/8443`
- App server → Vault `8200`
- App server → Storage `9000` və ya `443`

## 28.4 Tam port matrisi (serverdən serverə)

Aşağıdakı cədvəl “split deployment” üçün minimum icazələri göstərir. `<SUBNET_APP>`, `<SUBNET_DMZ>` kimi yerləri sizin şəbəkəyə uyğunlaşdırın.

| Mənbə (source)                                            | Hədəf (destination)      |                        Port | Nə üçün                             |
| --------------------------------------------------------- | ------------------------ | --------------------------: | ----------------------------------- |
| User Internet / Corporate Users                           | Nginx (DMZ)              |                      80/tcp | HTTP (redirect üçün)                |
| User Internet / Corporate Users                           | Nginx (DMZ)              |                     443/tcp | HTTPS əsas trafik                   |
| Jump/Office Admin IP                                      | Bütün serverlər          |                      22/tcp | SSH admin giriş                     |
| Nginx (DMZ)                                               | Docs5 App (APP subnet)   | 5000/tcp və ya tətbiq portu | Reverse proxy → upstream            |
| Docs5 App (APP subnet)                                    | PostgreSQL (DB subnet)   |                    5432/tcp | DB connection                       |
| Docs5 App (APP subnet)                                    | Keycloak (AUTH subnet)   |     8080/tcp və ya 8443/tcp | Token/auth əməliyyatları            |
| Docs5 App (APP subnet)                                    | Vault (SEC subnet)       |                    8200/tcp | Secret/config oxuma                 |
| Docs5 App (APP subnet)                                    | Storage (STORAGE subnet) |      9000/tcp və ya 443/tcp | Fayl upload/download                |
| Preview/Migration util serverləri (əgər ayrıca işləyirsə) | Eyni hədəflər            |                eyni portlar | util-lər də DB/storage/auth-a çıxır |

> Qeyd: App server portu (cədvəldə `5000`) tam sizin Kestrel/compose konfiqinizdən asılıdır. Bəzi qurulumlarda Nginx app-ə `127.0.0.1:5000` ilə gedir (eyni host), bəzi qurulumlarda ayrı hosta gedir.

## 28.5 UFW (və ya iptables) səviyyəsində nümunə qaydalar

Bu hissə konseptual nümunədir (placeholder IP-lərlə). Prod-da dəyişiklik yalnız approval/proseslə.

### 28.5.1 Nginx server (DMZ) üçün

- İncoming:
  - allow `80/tcp`, `443/tcp` (internet/LB source-lardan)
  - allow `22/tcp` (yalnız jump/office IP)
- Outgoing:
  - App serverə upstream port (məs: `5000/tcp`)

Nümunə:

```bash
sudo ufw allow from <OFFICE_IP>/32 to any port 22 proto tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
# upstream üçün outbound adətən açıq olur; məhdud mühitdə ayrıca allow lazım ola bilər.
```

### 28.5.2 App server üçün

- İncoming:
  - upstream port (`5000/5001` və ya sizin port) yalnız Nginx server IP-dən
  - `22/tcp` yalnız admin IP-dən
- Outgoing:
  - DB `5432`
  - Keycloak `8080/8443`
  - Vault `8200`
  - Storage `9000/443`

Nümunə:

```bash
sudo ufw allow from <NGINX_IP>/32 to any port 5000 proto tcp
sudo ufw allow from <OFFICE_IP>/32 to any port 22 proto tcp
```

### 28.5.3 DB server üçün

- İncoming:
  - `5432/tcp` yalnız App server IP/subnet-dən
  - `22/tcp` yalnız admin IP

```bash
sudo ufw allow from <APP_SUBNET> to any port 5432 proto tcp
sudo ufw allow from <OFFICE_IP>/32 to any port 22 proto tcp
```

> DB portunu internetə açmaq olmaz. pgAdmin üçün “internetdən 5432 açmaq” əvəzinə SSH tunnel istifadə edin (Bölmə 23).

## 28.6 Sizin mühit üçün “tam və dəqiq” port siyahısını çıxartmaq (5 dəqiqəlik prosedur)

Aşağıdakı addımların məqsədi: “bizim arxitektura” üzrə **real** portları (5000 yerinə bəlkə 7000, 9000 yerinə 443 və s.) dəqiq tapmaq.

### Addım 1: hər serverdə dinləyən portları çıxart

```bash
sudo ss -tulnp
```

Fokus olun:

- `:80`, `:443` (nginx)
- `:5432` (postgres)
- `:8080`, `:8443` (keycloak)
- `:8200` (vault)
- `:9000` (minio/storage)
- app portları (`:5000`, `:5001` və s.)

### Addım 2: Docker konteyner port publish-lərini yoxla (əgər container-dirsə)

```bash
docker ps --format 'table {{.Names}}\t{{.Ports}}'
```

Buradan görəcəksiniz:

- host port → container port mapping

### Addım 3: Nginx upstream portunu dəqiq tap

Nginx config-də `proxy_pass` sətrinə baxın:

- `proxy_pass http://127.0.0.1:5000;`
- və ya `proxy_pass http://10.0.10.25:7000;`

Axtarış:

```bash
sudo grep -R "proxy_pass" /etc/nginx -n
```

### Addım 4: App konfiqində DB host/port-a bax

App `appsettings.json` və ya env-də DB host/port olur. Bu workspace-də util-lərin appsettings-lərində port `5432` görünür.

Tətbiq serverində:

```bash
grep -R "Port=5432" -n /opt/wss 2>/dev/null
```

### Addım 5: Çıxan “real portlar” ilə cədvəli yenilə

- Cədvəldəki “5000/5001” kimi yerləri sizin real portlarla əvəz edin.
- Hər rule üçün source IP/subnet-i məhdudlaşdırın.

## 28.7 BA üçün praktiki qayda

- **DB portu (`5432`) internetə açılmır** — yalnız app subnet/jump ilə.
- “İşləmir” deyəndə ilk sual: **port dinləyir?** (`ss -tulnp`) və **firewall bloklayır?** (`ufw status verbose`).
