# Bölmə 6. Fayllarla və qovluqlarla işləmək

Bu bölmədə serverdə fayl/qovluq əməliyyatlarını təhlükəsiz icra etməyi öyrənəcəksiniz. Məqsəd: “kopyaladım, itdi” tipli riskləri azaltmaq.

## 6.1 `mkdir`

Qovluq yaratmaq:

```bash
mkdir logs
mkdir -p /tmp/docs5/debug
```

- `-p` ara qovluqları da yaradır.

## 6.2 `touch`

Boş fayl yaratmaq və ya mövcud faylın timestamp-ini yeniləmək:

```bash
touch notes.txt
```

## 6.3 `cp`

Fayl kopyalama:

```bash
cp appsettings.json appsettings.json.bak
```

Qovluq kopyalama (recursive):

```bash
cp -r /opt/wss/docs /opt/wss/docs.bak
```

## 6.4 `mv`

Köçürmə və ya rename:

```bash
mv appsettings.json appsettings.json.old
mv file.txt /tmp/
```

## 6.5 `rm`

Silmə:

```bash
rm file.txt
```

Qovluq silmək üçün (daxili ilə birlikdə):

```bash
rm -r folder
```

## 6.6 `rm -rf` riskləri

`rm -rf`:

- soruşmadan silir (`-f`)
- qovluğu içi ilə silir (`-r`)

Prod-da bu əməliyyat **ən riskli** əmrlərdəndir.

Minimal intizam:

1. Əvvəl `ls` ilə nə sildiyini gör
2. Wildcard varsa, daha da diqqətli ol
3. Əmin deyilsənsə, etmə

## 6.7 duplicate/backup copy yaratmaq

Config dəyişməzdən əvvəl backup “qızıl qayda”dır:

```bash
cp nginx.conf nginx.conf.bak_$(date +%F_%H%M)
```

> Əgər `date` formatı çətin gəlirsə, ən azından manual `.bak` yaratmaq belə faydalıdır.

## 6.8 recursive copy

Böyük qovluqları kopyalayan zaman:

```bash
cp -r source_dir/ dest_dir/
```

Risk:

- disk doluluğu
- kopya vaxt aparır

Kopyadan əvvəl ölçünü yoxlamaq faydalıdır:

```bash
du -sh source_dir
```

## 6.9 qovluq strukturunu köçürmək

Əgər yalnız struktur lazımdırsa (fayllar yox), `find` ilə:

```bash
cd source_dir
find . -type d -print0 | xargs -0 -I{} mkdir -p "../dest_dir/{}"
```

> Bu tip komandalar prod-da ehtiyatla işlədilməlidir. Əmin deyilsinizsə, DevOps ilə edin.

## 6.10 fayl axtarmaq

### `find`

Qovluqda fayl tapmaq:

```bash
find /opt/wss -name "docker-compose.yml"
find . -type f -name "*.json"
```

### `locate`

`locate` sürətlidir, amma database yenilənməlidir (hər serverdə olmaya bilər).

## 6.11 fayl içində mətn axtarmaq

### `grep`

Fayl içində “sətir tap”:

```bash
grep "proxy_pass" /etc/nginx/nginx.conf
```

### `grep -r`

Qovluq daxilində recursive axtarış:

```bash
grep -R "server_name" /etc/nginx/
```

Çıxış çoxdursa:

```bash
grep -R "server_name" /etc/nginx/ | head
```

## 6.12 fayl ölçüsü və tarixçəsini görmək

- ölçü və tarix: `ls -lah`
- qovluq ölçüsü: `du -sh`

Misal:

```bash
ls -lah appsettings.json
stat appsettings.json
```

---

Növbəti bölmə fayla baxmaq və redaktə (cat/less/head/tail/nano/vim) və config dəyişəndə nəzarət addımlarını detallandırır.
