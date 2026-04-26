# Bölmə 23. PostgreSQL-ə qoşulma: pgAdmin və digər üsullar

Bu bölmədə məqsəd: BA-nın PostgreSQL-ə **təhlükəsiz** şəkildə qoşulub minimal yoxlamaları edə bilməsidir.

## 23.1 PostgreSQL connection anlayışı

Bir DB connection üçün əsas parametrlər:

- host
- port (default 5432)
- database name
- username
- password

## 23.2 host, port, db name, username, password

Nümunə (placeholder):

- host: `10.0.10.30`
- port: `5432`
- db: `DBF_Content`
- user: `wss_docs_db`

> Vacib: Password/connection string-ləri açıq mətnlə paylaşmayın.

## 23.3 pgAdmin nədir

pgAdmin:

- GUI vasitəsilə PostgreSQL idarəetmə aləti
- query yazmaq, table-lara baxmaq, connection test etmək olar

## 23.4 pgAdmin ilə yeni server connection yaratmaq

1. `Register -> Server`
2. `General` tab: Name verin (məs: `prod-db (via ssh)`)
3. `Connection` tab: host/port/db/user daxil edin

## 23.5 pgAdmin-də General və Connection hissələri

- General: yalnız ad və qruplama
- Connection: real texniki parametrlər

## 23.6 SSH tunnel ilə qoşulma məntiqi

DB server çox vaxt birbaşa açıq olmur. Onda:

- lokal kompüterdən jump/bastion-a SSH
- jump serverdən DB-yə daxili şəbəkə ilə çıxış

Bu “tunnel” yaradır: siz lokalda `localhost:xxxx`-ə qoşulursunuz, trafikiniz DB-yə gedir.

## 23.7 Nə zaman SSH tunnel lazımdır

- DB private subnet-dədir
- firewall yalnız jump serverdən icazə verir

## 23.8 pgAdmin içində SSH tunneling konfiqurasiyası

pgAdmin versiyasına görə menyu fərqli ola bilər, amma məntiq eynidir:

- SSH Host: jump server
- SSH Username: jump user
- SSH Key/Password: authentication
- DB Host: daxildəki DB host (bəzən `127.0.0.1` jump tərəfindən)

## 23.9 local port forwarding məntiqi

CLI ilə örnək:

```bash
ssh -L 15432:10.0.10.30:5432 ba@jump.company.az
```

Sonra pgAdmin-də:

- host: `127.0.0.1`
- port: `15432`

## 23.10 connection failure halları

- `timeout` → routing/firewall/VPN
- `password authentication failed` → credential
- `no pg_hba.conf entry` → DB access policy

## 23.11 `psql` ilə alternativ qoşulma

Serverdə `psql` varsa:

```bash
psql -h 10.0.10.30 -p 5432 -U wss_docs_db -d DBF_Content
```

Password soruşa bilər.

Təhlükəsiz yanaşma:

- prod-da mümkün olduqda read-only user istifadə edin

## 23.12 DB list və table list yoxlaması

`psql` içində:

- DB list: `\l`
- table list: `\dt`

## 23.13 readonly işləmək intizamı

BA üçün əsas:

- `SELECT` + `LIMIT` ilə yoxla
- `UPDATE/DELETE` etmə
- şübhəli script-ləri prod-da işlətmə

---

Növbəti bölmə `psql` ilə baza səviyyəsində işləmək: `\c`, `\dt`, basic select, limit, migration zamanı yoxlamalar və connection refusal səbəbləri.
