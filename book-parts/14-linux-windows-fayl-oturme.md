# Bölmə 14. Linux-dan Windows-a və Windows-dan Linux-a fayl ötürmə

Bu bölmə BA üçün ən çox lazım olan “logu serverdən götürüb Windows-da paylaşmaq” ssenarisini sadələşdirir.

## 14.1 Windows-da `scp` istifadəsi

Müasir Windows 10/11-də OpenSSH client çox vaxt hazır gəlir.

PowerShell/CMD-də:

```powershell
scp user@10.0.10.25:/var/log/nginx/error.log .
```

## 14.2 PowerShell üzərindən `scp`

Port fərqlidirsə:

```powershell
scp -P 2222 user@10.0.10.25:/var/log/nginx/error.log .
```

Key ilə:

```powershell
scp -i C:\Users\you\.ssh\id_rsa user@10.0.10.25:/tmp/report.txt .
```

## 14.3 Linux serverdən Windows-a fayl endirmək

Windows tərəfdə `scp` ilə endirmək adətən daha rahatdır.

Alternativ:

- WinSCP (Bölmə 4)

## 14.4 Windows-dan Linux serverə fayl atmaq

```powershell
scp .\appsettings.json user@10.0.10.25:/tmp/
```

Prod-da risk:

- config faylını yanlış yerə atmaq
- overwrite etmək

## 14.5 path format fərqləri

- Windows: `C:\Users\you\file.txt`
- Linux: `/home/you/file.txt`

PowerShell-də qaçış simvollarına diqqət:

- `\` iki backslash ola bilər

## 14.6 tipik `scp` xətaları

- `Permission denied` → user/key problem
- `Connection timed out` → VPN/firewall/jump tələb olunur
- `No such file or directory` → path səhvdir (Linux path-ları case-sensitive-dir)

## 14.7 böyük fayllar üçün tövsiyələr

- əvvəl arxiv edin (`tar.gz` və ya `zip`)
- checksum ilə təsdiqləyin
- mümkün olduqda `rsync` istifadə edin (Linux tərəfdə)

---

Növbəti bölmə “müvəqqəti fayl paylaşımı” üçün Python HTTP server + `wget` yanaşmasını izah edir.
