# Bölmə 17. Docker əsasları

Docs5-in bir çox yerləşdirməsində servis(lər) Docker konteynerlərində işləyir. BA üçün məqsəd:

- konteynerlərin vəziyyətinə baxmaq
- log oxumaq
- minimal restart/stop/start fərqini başa düşmək

## 17.1 Docker nədir

Docker tətbiqləri “container” içində işlətməyə imkan verir.

- container → izolyasiya olunmuş proses mühiti
- image → container-in “şablonu”

## 17.2 image, container, volume, network anlayışı

- **Image**: hazır paket (OS layer + app)
- **Container**: image-dən işləyən instans
- **Volume**: persistent data (DB data, upload file, və s.)
- **Network**: container-lər arası əlaqə

## 17.3 container və VM fərqi

Sadə izah:

- VM: ayrıca virtual OS
- Container: eyni host kernel üstündə izolyasiya

Praktik nəticə:

- container daha yüngül və sürətlidir
- amma host resurslarına bağlıdır

## 17.4 Docs5 niyə docker üzərində qurulur

- deploy daha standart olur
- servis-ləri ayrılıqda idarə etmək asanlaşır
- upgrade/migration util-lərini ayrıca konteyner kimi işə salmaq mümkündür

Bu repodakı `Sources/utils/*/docker-compose.yml` fayllarında “dotnet util” konteynerinin bir dəfə işləyib çıxması nümunələri var.

## 17.5 `docker version` və `docker info` yoxlaması

```bash
docker version
docker info
```

BA üçün:

- docker işləyirmi?
- host resurs limitləri varmı?

## 17.6 `docker ps`

İşləyən konteynerlər:

```bash
docker ps
```

## 17.7 `docker ps -a`

Hamısı (dayananlar da):

```bash
docker ps -a
```

Triage:

- konteyner tez-tez restart edirsə → problem var

## 17.8 `docker images`

Image siyahısı:

```bash
docker images
```

## 17.9 container lifecycle

Əsas əmrlər:

```bash
docker start <container>
docker stop <container>
docker restart <container>
docker rm <container>
```

Risk qeydi:

- `rm` konteyneri silir; volume silinmirsə data qalır, amma yenə də ehtiyatlı olun.

## 17.10 `docker inspect`

Konteynerin detalları (network, mount, env):

```bash
docker inspect <container>
```

Buradan:

- hansı volume mount edilib
- hansı IP/port-lar var

tapmaq olar.

## 17.11 `docker stats`

Konteyner resurs istifadəsi:

```bash
docker stats
```

CPU/RAM limit problemi şübhəsi varsa faydalıdır.

---

Növbəti bölmə Docker Compose: servis-ləri birlikdə qaldırmaq/endirmək, log/ps/config, rebuild halları və tipik problemlər.
