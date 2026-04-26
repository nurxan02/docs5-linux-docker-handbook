# Bölmə 10. Linux şəbəkə əsasları

Şəbəkə problemləri çox vaxt “servis down” kimi görünür, amma səbəb DNS/firewall/routing olur. Bu bölmə BA üçün minimal şəbəkə triage verir.

## 10.1 IP, port, hostname

- **IP**: serverin şəbəkə ünvanı
- **Port**: eyni IP-də fərqli servisləri ayırmaq üçün “qapı” (məs: 22 SSH, 443 HTTPS)
- **Hostname**: DNS adı (məs: `docs.company.az`)

## 10.2 private subnet nədir

Private subnet adətən internetə açıq olmayan daxili şəbəkədir:

- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.0.0/16

Bu şəbəkələrə çatmaq üçün:

- VPN
- jump/bastion
- daxili routing

lazım ola bilər.

## 10.3 eyni subnetdə iki server necə danışır

Eyni subnetdə (məs: 10.0.10.x) serverlər adətən birbaşa danışa bilir (firewall bloklamırsa).

Fərqli subnetdə isə routing qaydası lazımdır.

## 10.4 `ping`

Network “ümumi əlçatımlılıq” yoxlaması:

```bash
ping -c 4 10.0.10.25
```

Qeyd:

- bəzən ping firewall tərəfindən bloklanır, ping işləmirsə bu mütləq “server down” demək deyil.

## 10.5 `curl`

HTTP/HTTPS endpoint yoxlaması:

```bash
curl -I https://docs.company.az
curl -v http://127.0.0.1:5000/health
```

`-I` yalnız header-ləri gətirir.

## 10.6 `ss -tuln`

Serverdə hansı portlar “listen” edir:

```bash
ss -tuln
```

Filtrləmək:

```bash
ss -tuln | grep 443
```

BA üçün fayda:

- Nginx 443-də dinləyirmi?
- App portu açıqdırmı?

## 10.7 traceroute və route anlayışı

`traceroute` (hər serverdə olmaya bilər) paketlərin hansı hop-lardan keçdiyini göstərir.

```bash
traceroute 10.0.10.25
```

Routing problemi şübhəsi varsa, DevOps/Network komandası ilə işləmək lazımdır.

## 10.8 local və remote bağlantı məntiqi

Eyni hostda iki nöqtə:

- local: `127.0.0.1` yalnız həmin serverin özündən görünür
- remote: serverin real IP-si ilə digər maşınlar da qoşula bilər (firewall icazə verirsə)

Misal:

- app `127.0.0.1:5000`-də dinləyirsə, Nginx eyni hostdadırsa proxy edə bilər.
- amma başqa serverdən birbaşa `:5000`-ə çıxmaq mümkün olmaya bilər.

---

Növbəti bölmə firewall və UFW: status yoxlama, port açmaq, source IP üzrə icazə, qayda silmək və təhlükəsiz qayda yazmaq prinsipləri.
