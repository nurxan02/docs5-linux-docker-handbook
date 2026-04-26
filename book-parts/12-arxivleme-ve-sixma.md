# Bölmə 12. Arxivləmə və sıxma əməliyyatları

Arxivləmə (tar/zip) ən çox bu işlər üçün lazım olur:

- backup hazırlamaq
- böyük qovluğu köçürmədən əvvəl “tək fayla” yığmaq
- Windows-a ötürmək üçün paketləmək

## 12.1 `tar` nədir

`tar` qovluq/faylları “arxiv” adı ilə bir fayla yığır. `tar` özü sıxma etmir — sadəcə paketləyir.

## 12.2 `tar.gz` nədir

`tar.gz`:

- əvvəl `tar` ilə paket
- sonra `gzip` ilə sıxma

Nəticə: daha kiçik fayl.

## 12.3 zip və tar fərqi

- Linux-da `tar.gz` daha çox istifadə olunur
- Windows-da `zip` daha rahat açıla bilər

Praktik:

- server-to-server transfer üçün `tar.gz` çox uyğundur

## 12.4 qovluğu tar etmək

```bash
tar -cvf logs.tar /var/log/nginx
```

- `c` create
- `v` verbose
- `f` file

## 12.5 `tar.gz` ilə compress etmək

```bash
tar -czvf logs.tar.gz /var/log/nginx
```

- `z` gzip

## 12.6 archive açmaq

```bash
tar -xzvf logs.tar.gz
```

Əgər başqa qovluğa açmaq istəsəniz:

```bash
mkdir -p /tmp/extract
tar -xzvf logs.tar.gz -C /tmp/extract
```

## 12.7 list məzmunu görmək

Açmadan içini görmək:

```bash
tar -tzf logs.tar.gz | head
```

## 12.8 backup üçün arxivləmə ssenariləri

Təhlükəsiz yanaşma:

- backup-u tarixlə adlandırın
- haradan alındığını adında qeyd edin

```bash
tar -czvf docs5_nginx_conf_$(date +%F_%H%M).tar.gz /etc/nginx
```

## 12.9 böyük qovluqları köçürmədən əvvəl yığmaq

Böyük qovluğu bir fayla yığanda:

- transfer daha stabil olur
- integrity check daha rahat olur (checksum)

Checksum nümunəsi:

```bash
sha256sum logs.tar.gz > logs.tar.gz.sha256
```

## 12.10 Windows-a ötürmək üçün arxiv hazırlamaq

Windows tərəfi üçün zip lazım ola bilər:

```bash
zip -r logs.zip /var/log/nginx
```

Əgər `zip` yoxdursa, qurmaq lazım ola bilər.

---

Növbəti bölmələr fayl transferinə keçir: `scp`, `rsync`, Windows↔Linux ssenariləri.
