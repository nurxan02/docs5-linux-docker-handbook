# Bölmə 13. Linux-dan Linux-a fayl ötürmə

Serverlər arası fayl ötürmənin ən tipik yolu SSH üstündən işləyən `scp`-dir. Daha böyük və davam edən transferlərdə `rsync` daha yaxşıdır.

## 13.1 `scp` nədir

`scp` (secure copy) SSH üstündən fayl kopyalayır.

## 13.2 bir serverdən digərinə `scp` ilə fayl atmaq

Lokaldan (sizdəki maşından) serverə:

```bash
scp file.txt user@10.0.10.25:/tmp/
```

Serverdən lokal maşına:

```bash
scp user@10.0.10.25:/var/log/nginx/error.log ./
```

Port fərqlidirsə:

```bash
scp -P 2222 file.txt user@10.0.10.25:/tmp/
```

## 13.3 qovluq ötürmək

Recursive:

```bash
scp -r /tmp/debug user@10.0.10.25:/tmp/
```

## 13.4 `rsync` giriş səviyyəsi

`rsync`:

- kəsilib davam edə bilir
- yalnız dəyişən hissəni ötürür
- daha stabildir

Sadə nümunə:

```bash
rsync -avz /tmp/debug/ user@10.0.10.25:/tmp/debug/
```

SSH port fərqlidirsə:

```bash
rsync -avz -e "ssh -p 2222" /tmp/debug/ user@10.0.10.25:/tmp/debug/
```

## 13.5 eyni subnetdə sürətli müvəqqəti transfer yanaşması

Eyni subnetdə böyük fayl ötürmədə:

- disk doluluğunu yoxlayın (`df -h`)
- əvvəl arxiv edin (`tar.gz`)
- checksum ilə təsdiqləyin

## 13.6 SSH üzərindən transfer riskləri

- səhv serverə göndərmək (mühit qarışır: test vs prod)
- overwrite risk
- secret-lı faylların (vault settings) yanlış paylaşımı

Minimal intizam:

- hədəf IP/hostname-i iki dəfə yoxlayın
- `/tmp` kimi müvəqqəti qovluqdan istifadə edin
- prod secret fayllarını mail/chat ilə paylaşmayın

---

Növbəti bölmə Windows↔Linux transfer ssenarilərini izah edir.
