# Bölmə 27. Storage əməliyyat məntiqi

Docs5-də fayllar iki hissə kimi düşünülür:

- metadata (DB-də: fayl adı, ölçü, id, link)
- blob data (storage-da: real binary məzmun)

Bu ayrım storage problemini daha tez tapmağa kömək edir.

## 27.1 WSS Storage nədir

Storage:

- fayl upload/download üçün servis
- bəzən S3-compatible, bəzən filesystem-based ola bilər

## 27.2 blob anlayışı

Blob — “binary large object”: PDF, DOCX, şəkil, arxiv və s.

## 27.3 storage metadata və blob data fərqi

Simptom nümunələri:

- UI faylı “görür”, amma download alınmır → metadata var, blob yoxdur/əlçatmazdır
- fayl ümumiyyətlə görünmür → metadata DB-də yoxdur və ya query problemi var

## 27.4 storage servisin ayaqda olub-olmadığını yoxlamaq

Yanaşma:

- container isə: `docker ps`, `docker logs`
- host servis isə: `systemctl status`

Endpoint varsa:

```bash
curl -I http://storage.internal/health
```

## 27.5 storage config faylları

Storage config yerləşimi mühitə görə dəyişir.

BA üçün praktik:

- config yolunu tapmaq üçün compose volume-larına baxın
- loglardan config path çıxara bilərsiniz

## 27.6 migration kontekstində storage

Storage migration ssenariləri:

- köhnə storage-dan yeniyə keçid
- path/namespace dəyişməsi

Bu mərhələdə:

- preview generator util-ləri
- file link update util-ləri

işlədilə bilər.

## 27.7 preview və file access problemləri

Preview açılmırsa:

- storage əlçatandır?
- preview generation servisi işləyir?
- fayl tipi dəstəklənirmi?

Triage:

- app loglarda preview error-a bax
- storage loglarda access denied/not found var?

## 27.8 storage ilə bağlı tipik səhvlər

- `AccessDenied` / `403` → credential/permission
- `NotFound` → blob yoxdur, path səhv
- `Timeout` → network/firewall

---

Növbəti bölmə portlar və firewall qaydalarıdır: komponentlər arası şəbəkə icazələrinin düzgün planlanması.
