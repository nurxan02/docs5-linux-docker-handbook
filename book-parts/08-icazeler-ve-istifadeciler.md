# Bölmə 8. İcazələr və istifadəçilər

“Permission denied” Linux-da ən çox rast gəlinən problemlərdən biridir. Bu bölmə icazə modelini sadə dildə izah edir.

## 8.1 user, group, owner anlayışı

Hər faylın:

- owner-i (istifadəçi)
- group-u
- permission set-i var

Yoxlama:

```bash
ls -la
```

## 8.2 `chmod`

Permission dəyişmək.

Rəqəmli format:

- `r = 4`, `w = 2`, `x = 1`
- `7 = rwx`, `6 = rw-`, `5 = r-x`, `4 = r--`

Tipik:

```bash
chmod 644 appsettings.json
chmod 755 /opt/wss/docs
```

## 8.3 `chown`

Owner/group dəyişmək:

```bash
sudo chown root:root /etc/nginx/nginx.conf
```

## 8.4 executable permission

Fayl “işlədilə bilən” olsun deyə `x` icazəsi lazımdır.

```bash
chmod +x script.sh
./script.sh
```

## 8.5 777 niyə təhlükəlidir

`777` deməkdir:

- hamı oxuya, yaza, icra edə bilər

Bu:

- təhlükəsizlik riskidir
- audit zamanı problem yaradır
- bəzən sistem davranışını dəyişir

## 8.6 755, 644 tipik halları

- qovluq: `755` (owner tam, digərləri oxu+icra)
- config faylı: `644` (owner oxu+yaz, digərləri oxu)

## 8.7 permission denied xətasının analizi

Ən faydalı 3 sual:

1. Hansı user-lə işləyirəm?

```bash
whoami
id
```

2. Faylın owner/permission-ı nədir?

```bash
ls -la /path/to/file
```

3. Proses hansı user-lə işləyir?

- docker konteynerində proses user-i fərqli ola bilər
- system service-də `systemctl status` məlumat verə bilər

Prod-da ümumi yanaşma:

- icazəni “hamıya açmaq” yox, düzgün user/group-a vermək

## 8.8 `sudo` və `sudo su`

- `sudo <command>` — tək komandanı admin kimi icra edir
- `sudo su` — root shell açır

Prod intizamı:

- mümkün qədər `sudo <command>` istifadə edin
- root shell-də yanlış əməl riski daha yüksəkdir

## 8.9 root session ilə işləmə intizamı

- root sessiya açanda məqsədi konkret olsun
- iş bitən kimi çıxın: `exit`
- paralel bir neçə root sessiya açmayın

---

Növbəti bölmə monitorinq: CPU/RAM/disk, proseslər, kill və sistemin “niyə yavaşladığını” ilkin səviyyədə anlama.
