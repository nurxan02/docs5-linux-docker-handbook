# Bölmə 22. Nginx və reverse proxy əsasları

Nginx Docs5 kimi sistemlərdə çox vaxt “giriş qapısı”dır. İstifadəçi domenə girir və ilk qarşılayan Nginx olur.

## 22.1 reverse proxy nədir

Reverse proxy:

- istifadəçi request-lərini qəbul edir
- daxildəki servisə yönləndirir
- TLS (HTTPS) terminasiya edə bilər
- cache, gzip, rate limit kimi funksiyalar verə bilər

## 22.2 upstream nədir

Upstream — Nginx-in yönləndirdiyi daxili hədəf.

Misal:

- `127.0.0.1:5000`
- `docs-app:5000` (docker network daxilində)

## 22.3 `proxy_pass`

Ən tipik direktiv:

```nginx
location / {
  proxy_pass http://127.0.0.1:5000;
}
```

Burada:

- Nginx request-i app portuna ötürür.

## 22.4 80 və 443 məntiqi

- 80 → HTTP
- 443 → HTTPS

Tipik pattern:

- 80-də gələn request 443-ə redirect edilir

## 22.5 SSL certificate və private key

HTTPS üçün:

- certificate (public)
- private key (gizli)

Private key təhlükəsizlik baxımından çox həssasdır:

- paylaşılmır
- repo-ya commit olunmur

## 22.6 `server_name`

Domen match:

```nginx
server {
  server_name docs.company.az;
}
```

Əgər `server_name` uyğun gəlmirsə:

- default server cavab verə bilər
- 404/incorrect site görünə bilər

## 22.7 nginx config test

Dəyişiklikdən sonra test:

```bash
sudo nginx -t
```

Uğurlu olsa:

```bash
sudo systemctl reload nginx
```

> `reload` config-i yenidən oxuyur, connection-ları daha az pozur. `restart` daha sərtdir.

## 22.8 common 502 səbəbləri

502 “Bad Gateway” deməkdir: Nginx upstream-dən düzgün cavab ala bilmir.

Tipik səbəblər:

- app servisi down
- upstream port dəyişib
- firewall/selinux bloklayır
- app crash edir (restart loop)

## 22.9 upstream connection refused

Nginx error log-da tipik görünüş:

- `connect() failed (111: Connection refused) while connecting to upstream`

Bu çox vaxt deməkdir:

- həmin portda heç nə dinləmir (`ss -tuln` ilə yoxlanır)

## 22.10 nginx log oxuma əsasları

Nginx log yerləri deploy-a görə dəyişir.

Host Nginx:

- `/var/log/nginx/access.log`
- `/var/log/nginx/error.log`

Konteyner Nginx:

- `docker logs nginx`

Triage qaydası:

- 502 → əvvəl error log
- 404/redirect → access log (request path, host header)

---

Növbəti bölmələr PostgreSQL-ə qoşulma (pgAdmin, SSH tunnel, psql) və təhlükəsiz read-only DB yoxlamalarıdır.
