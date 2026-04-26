# Bölmə 9. Sistem resursları və monitorinq

Incident zamanı ilk suallardan biri: “problem tətbiqdədir, yoxsa server resurslarında?” Bu bölmə BA üçün resurs yoxlamasını sadə edir.

## 9.1 `top`

CPU/RAM istifadə edən prosesləri canlı göstərir:

```bash
top
```

Çıxış: `q`.

## 9.2 `htop`

`top`-un daha rahat variantı (hər serverdə olmaya bilər):

```bash
htop
```

## 9.3 `free -m`

RAM vəziyyəti:

```bash
free -m
```

Nəyə baxmalı:

- available memory çox azdır?
- swap istifadə olunur?

## 9.4 `df -h`

Disk doluluğu:

```bash
df -h
```

Disk 90–95%+ doludursa:

- DB yavaşlaya bilər
- container logları şişə bilər
- servis yazma əməliyyatlarında qıra bilər

## 9.5 `du -sh`

Qovluq ölçüsü:

```bash
du -sh /var/log
```

Problemi tapmaq üçün:

```bash
du -sh /var/log/* | sort -h | tail
```

## 9.6 `uptime`

Serverin neçə gündür işlədiyini və load average-i göstərir:

```bash
uptime
```

Load average yüksəkdirsə, CPU və ya I/O bottleneck ola bilər.

## 9.7 `ps aux`

Proses siyahısı:

```bash
ps aux | head
ps aux | grep nginx
```

## 9.8 `kill` və `kill -9`

- `kill <pid>` — prosesi “yumşaq” dayandırmağa çalışır
- `kill -9 <pid>` — prosesi zorla öldürür

Prod üçün xəbərdarlıq:

- `kill -9` data korlanmasına səbəb ola bilər.
- BA təkbaşına process kill etməməlidir (prosedurla).

## 9.9 CPU, RAM, disk bottleneck necə anlaşılır

Sadə triage:

- **CPU**: `top`/`htop`-da 100% yaxın, load yüksək
- **RAM**: `free -m`-də available çox az, swap artır
- **Disk**:
  - `df -h` doludur
  - `iowait` `top`-da yüksəktir

Simptomlar:

- UI yavaş
- timeouts
- DB query-lər uzun çəkir

## 9.10 disk dolması və sistemə təsiri

Disk dolanda:

- log yazmaq mümkün olmur
- DB transaction-lar fail ola bilər
- docker overlay storage problem yarada bilər

BA üçün ən təhlükəsiz addımlar:

- `df -h` ilə təsdiqlə
- hansı qovluq şişib `du` ilə tap
- silmə əməliyyatına tələsmə (log silmək audit riskidir)
- DevOps ilə razılaşdır

---

Növbəti bölmə şəbəkə əsasları: IP/port/hostname, ping/curl, port dinləmə (`ss -tuln`), traceroute və local/remote bağlantı məntiqi.
