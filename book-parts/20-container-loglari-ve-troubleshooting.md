# Bölmə 20. Container logları və troubleshooting

Konteyner “ayaqdadır” demək “servis işləyir” demək deyil. Burada məqsəd logdan root cause tapmaq üçün sistematik yanaşmadır.

## 20.1 `docker logs`

Konteynerin stdout/stderr logları:

```bash
docker logs <container_name>
```

## 20.2 `docker logs -f`

Canlı izləmə:

```bash
docker logs -f <container_name>
```

## 20.3 son sətirləri görmək

```bash
docker logs --tail 200 <container_name>
```

## 20.4 restart loop-ları anlamaq

Əlamətlər:

- `docker ps`-də `Restarting` status
- uptime çox kiçik, restart count artır

Nə etmək:

- `docker logs --tail 200` ilə son error-u tap
- config/secret çatışmır?
- port conflict?

## 20.5 exit code anlayışı

Konteyner çıxanda exit code verir.

Baxmaq:

```bash
docker inspect <container_name> --format '{{.State.ExitCode}}'
```

Tipik:

- `0` → uğurlu çıxış
- `1` → ümumi error

## 20.6 “Restarting (139)” kimi halların mənası

`139` çox vaxt segmentation fault (crash) kimi anlaşılır.

Tipik səbəblər:

- native library problemi
- memory corruption
- image uyğunluğu

BA üçün addımlar:

- logları saxla
- son deploy dəyişiklikləri soruş
- DevOps/Dev-ə eskalasiya

## 20.7 logdan root cause tapmaq

Yaxşı yanaşma:

1. Ən son error sətrini tap
2. Error-un kontekstini (10–20 sətir əvvəl/sonra) çıxar
3. Timestamp-ə bax
4. “config path”, “connection string”, “permission denied” kimi açar sözlər axtar

## 20.8 app log vs proxy log vs system log

- Nginx logları: `/var/log/nginx/access.log`, `/var/log/nginx/error.log`
- App logları: container stdout (`docker logs`) və ya app içi log
- System log: `journalctl` (systemd olan serverlərdə)

Simptom → log:

- 502 → əvvəl Nginx error.log
- DB error → app log
- disk dolu → system log + `df -h`

## 20.9 container daxilində müvəqqəti debug

Əgər container dərhal çıxırsa:

- entrypoint/command səhv ola bilər
- config mount olunmur

Compose ilə debug üçün bəzən:

- `docker compose run --entrypoint sh <service>` kimi yanaşmalar istifadə olunur (prosedurla).

## 20.10 dəyişikliklərin persistent olub-olmaması

Troubleshooting zamanı:

- “konteyner içində düzəltdim” həlli prod üçün risklidir
- düzəliş deploy/compose səviyyəsində edilməlidir

---

Növbəti bölmələr Docs5 qovluq/servis strukturu, Nginx reverse proxy, PostgreSQL qoşulma və DB əməliyyatları, Postman testləri və auth (Vault/Keycloak) mövzularına keçəcək.
