# Bölmə 18. Docker Compose ilə işləmək

Docker Compose bir neçə konteyneri “bir paket” kimi idarə etmək üçündür. Docs5 kimi sistemlərdə Nginx, app, DB, Keycloak, util-lər çox vaxt compose ilə ayağa qaldırılır.

## 18.1 `docker compose` nədir

Compose sizə `docker-compose.yml` faylında servis-ləri təsvir edib, sonra bir komanda ilə hamısını işə salmağa imkan verir.

## 18.2 `docker-compose` və `docker compose` fərqi

- `docker compose` → Docker CLI-nin yeni inteqrasiyası
- `docker-compose` → köhnə standalone binary

Bu kitabçada əsasən `docker compose` formasını istifadə edirik.

## 18.3 compose file strukturu

Tipik:

- `services`: konteynerlər
- `volumes`: persistent data
- `networks`: şəbəkələr

## 18.4 `services`

Hər servis:

- `image` və ya `build`
- `environment`
- `volumes`
- `ports`
- `depends_on`

## 18.5 `ports`

Port mapping nümunəsi:

- `443:443` → host 443 → container 443

## 18.6 `environment`

Config çox vaxt environment variable-lərlə verilir. Secret-ları `.env` və ya Vault ilə idarə etmək daha doğrudur.

## 18.7 `volumes`

Volume:

- DB data itmesin deyə
- upload/storage data qalıcı olsun deyə

## 18.8 `networks`

Servislər eyni network-dədirsə, bir-birini servis adı ilə görə bilər.

## 18.9 `docker compose up -d`

Servisləri background-da qaldırmaq:

```bash
docker compose up -d
```

## 18.10 `docker compose down`

Servisləri endirmək:

```bash
docker compose down
```

> Qeyd: `down` şəbəkəni də silə bilər. Volume-ların silinməsi ayrıca flag-larla olur; yenə də ehtiyatlı olun.

## 18.11 `docker compose stop`

Servisləri dayandırır, amma “təsvir” saxlanır:

```bash
docker compose stop
```

## 18.12 `docker compose restart`

```bash
docker compose restart
```

Restart “müvəqqəti fix” kimi cazibədardır, amma root cause tapmadan prod-da ardıcıl restart etmək düzgün deyil.

## 18.13 `docker compose logs -f`

Logları izləmək:

```bash
docker compose logs -f
```

Tək servis:

```bash
docker compose logs -f nginx
```

## 18.14 `docker compose ps`

Compose servis status:

```bash
docker compose ps
```

## 18.15 config dəyişikliyindən sonra nə etmək

Dəyişiklik növünə görə:

- yalnız mount olunan fayl dəyişibsə → bəzən restart yetər
- image/build dəyişibsə → `up -d` (bəzən `--build`)

Təhlükəsiz ardıcıllıq:

1. backup
2. config validate (mümkündürsə)
3. kontrollu restart
4. log + health check

## 18.16 rebuild halları

```bash
docker compose up -d --build
```

## 18.17 orphan container və digər tipik problemlər

Orphan container:

- compose faylından servis silinib, amma köhnə konteyner qalıb

Həll:

```bash
docker compose up -d --remove-orphans
```

### Repo-dakı util compose nümunəsi

Bu repoda util-lər üçün compose nümunələri var (sadə “bir dəfə işləyib çıxan” konteyner):

```yaml
services:
  wssutil:
    image: images.wss-consulting.ru/dot/docs/prod:latest
    restart: "no"
    volumes:
      - "./appsettings.json:/app/appsettings.json:ro"
      - "../../vault.settings.json:/app/vault.settings.json:ro"
    entrypoint: ["dotnet", "util.dll"]
```

BA üçün nəticə:

- bu util-lər `up` edilib log oxunur
- sonra konteyner exit code-a baxılır

---

Növbəti bölmə container daxilində işləmək: `docker exec`, bash vs sh, container içindən config/log tapmaq və `docker cp` ilə fayl köçürmək.
