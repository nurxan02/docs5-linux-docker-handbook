# Bölmə 24. PostgreSQL ilə baza səviyyəsində işləmək

Bu bölmə BA-ya minimal DB əmrlərini verir ki, incident/migration zamanı “DB yaşayırmı?” sualını cavablaya bilsin.

## 24.1 `\l`

DB siyahısı:

```sql
\l
```

## 24.2 `\c`

DB seçmək:

```sql
\c DBF_Content
```

## 24.3 `\dt`

Cədvəl siyahısı:

```sql
\dt
```

Schema-larla:

```sql
\dt public.*
```

## 24.4 basic select

Təhlükəsiz select:

```sql
SELECT * FROM some_table LIMIT 10;
```

## 24.5 `LIMIT` ilə təhlükəsiz yoxlama

BA üçün qayda:

- prod-da həmişə `LIMIT` istifadə edin
- çox böyük table-larda `SELECT *` etməyin

## 24.6 update/delete riskləri

`UPDATE/DELETE`:

- data dəyişir
- audit və rollback çətinləşir

BA üçün: icazəniz olsa belə, prod-da tək etməyin.

## 24.7 migration zamanı DB-lərin yoxlanması

Migration-dan əvvəl:

- DB connection işləyir?
- disk dolu deyil?
- backup var?

Migration-dan sonra:

- əsas cədvəllər var?
- tətbiq login olur?
- loglarda DB error azalıb?

## 24.8 owner və permission anlayışı

DB-də də icazə var:

- hansı user hansı schema/table-ı oxuya/yaza bilər

Əgər read-only user ilə belə “permission denied” gəlirsə:

- DBA/DevOps ilə role/grant yoxlanmalıdır.

## 24.9 connection refusal, pg_hba, listen_addresses əsasları

DB-yə qoşulma alınmırsa:

- DB servisi işləyirmi?
- port dinləyirmi?
- firewall açıqdır?

PostgreSQL tərəfində:

- `listen_addresses` (hansı IP-lərdə dinləyir)
- `pg_hba.conf` (kimə icazə verir)

BA üçün praktik:

- bu faylları dəyişmək BA işi deyil
- ancaq simptomları düzgün toplamaq eskalasiya üçün vacibdir

---

Növbəti bölmə Postman ilə API yoxlamalarıdır.
