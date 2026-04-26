# Bölmə 11. Firewall və UFW

Firewall şəbəkə trafikinə “icazə ver / blokla” qaydaları tətbiq edir. Docs5 kimi sistemlərdə port problemi çox vaxt firewall ilə bağlı olur.

> Vacib: Prod-da firewall qaydası dəyişmək yüksək riskli əməliyyatdır. BA bunu adətən tək etmir; burada məqsəd **oxuma/yoxlama** və problemi düzgün anlamaqdır.

## 11.1 firewall nədir

Firewall:

- hansı portdan giriş/çıxış icazəlidir
- hansı IP-lər qoşula bilər
- default olaraq bloklayır və ya icazə verir (policy)

## 11.2 UFW nədir

UFW (Uncomplicated Firewall) Ubuntu/Debian-da firewall idarəsi üçün sadə interfeysdir.

## 11.3 `ufw status`

Status və qaydalar:

```bash
sudo ufw status
sudo ufw status verbose
```

## 11.4 `ufw enable / disable`

```bash
sudo ufw enable
sudo ufw disable
```

Prod qeydi:

- `disable` etmək təhlükəlidir (bütün açıq portlar exposed ola bilər).

## 11.5 port açmaq

Məsələn, 443 (HTTPS) açmaq:

```bash
sudo ufw allow 443/tcp
```

SSH üçün diqqət:

- SSH portunu bağlasanız, serverə girişi itirə bilərsiniz.

## 11.6 source IP üzrə icazə vermək

Yalnız müəyyən IP-dən icazə:

```bash
sudo ufw allow from 10.0.10.10 to any port 5432 proto tcp
```

Bu yanaşma daha təhlükəsizdir.

## 11.7 qaydanı silmək

Ən rahat:

```bash
sudo ufw status numbered
```

Sonra nömrə ilə silmək:

```bash
sudo ufw delete 3
```

## 11.8 default policy

Default policy:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Bu, tipik “təhlükəsiz default”dur.

## 11.9 deny vs allow

- `allow` — icazə verir
- `deny` — bloklayır

Bəzən qaydaların sırası və daha spesifik qayda davranışı dəyişir; buna görə “niyə yenə bağlanmır?” halları ola bilər.

## 11.10 firewall problemi necə anlaşılır

Simptomlar:

- eyni serverdə servis işləyir, amma başqa serverdən qoşulmaq olmur
- `connection timed out`

Yoxlama yanaşması:

1. Servis portda dinləyirmi?

```bash
ss -tuln | grep 5432
```

2. UFW status nədir?

```bash
sudo ufw status verbose
```

3. Local yoxlama:

```bash
curl -v http://127.0.0.1:5000
```

Local işləyib remote işləmir → firewall/routing ehtimalı artır.

## 11.11 servis açıqdır, amma port niyə görünmür

Səbəblər:

- servis yalnız `127.0.0.1`-ə bind olub
- docker port publish edilməyib
- firewall inbound bloklayır
- security group / network ACL (cloud mühitlərdə)

## 11.12 təhlükəsiz qayda yazma prinsipləri

- mümkün qədər dar icazə (yalnız lazım olan port, yalnız lazım olan source IP)
- dəyişiklikdən əvvəl təsir analiz
- dəyişiklikdən sonra test:
  - port açıldı?
  - kənardan qoşulma oldu?
- rollback planı (qaydanı silmək)

---

Növbəti bölmələr arxivləmə, fayl transferi, wget/curl, Docker/Compose və container troubleshooting mövzularına keçəcək.
