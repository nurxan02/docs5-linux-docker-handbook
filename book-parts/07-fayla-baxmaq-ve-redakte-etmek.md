# Bölmə 7. Fayla baxmaq və redaktə etmək

Bu bölmədə məqsəd: serverdə config və log fayllarını “minimum risklə” oxumaq və lazım gələrsə redaktə etmək.

## 7.1 `cat`

Kiçik fayllar üçün:

```bash
cat appsettings.json
```

Risk:

- böyük log faylını `cat` etmək terminalı “daşır”. Böyük faylda `less` istifadə edin.

## 7.2 `less`

Böyük fayllar üçün ən rahat:

```bash
less /var/log/nginx/error.log
```

İdarəetmə:

- `q` → çıxış
- `/text` → axtar
- `n` → növbəti uyğunluq

## 7.3 `more`

`less`-in sadə alternatividir. Adətən `less` daha yaxşıdır.

## 7.4 `head`

Faylın başlanğıcı:

```bash
head -n 50 file.log
```

## 7.5 `tail`

Faylın sonu:

```bash
tail -n 200 file.log
```

## 7.6 `tail -f` ilə log izləmək

Canlı log izləmək:

```bash
tail -f /var/log/nginx/error.log
```

Qısa yol ilə dayandırmaq: `Ctrl+C`.

Triage zamanı tövsiyə:

- problem replika edilən anda `tail -f` açıq saxlayın
- timestamp-ə diqqət edin

## 7.7 `nano` ilə redaktə

Sadə editor:

```bash
nano appsettings.json
```

Əsas düymələr:

- `Ctrl+O` → yaz (save)
- `Ctrl+X` → çıx

Prod-da:

- redaktə etməzdən əvvəl backup yaradın (Bölmə 6.7)

## 7.8 `vim` ilə baza səviyyəsində iş

Bəzi serverlərdə `nano` olmaya bilər, amma `vim` olar.

Əsas:

- aç: `vim file`
- edit rejimi: `i`
- çıx/saxla: `Esc` sonra `:wq`
- çıx (saxlamadan): `Esc` sonra `:q!`

## 7.9 config faylını dəyişəndə nəzarət addımları

Config dəyişmə “dəyişiklik”dir. Minimal nəzarət ardıcıllığı:

1. Dəyişiklikdən əvvəl backup:

```bash
cp config.json config.json.bak
```

2. Dəyişiklikləri kiçik saxla (bir dəfəlik böyük refactor etmə).
3. Əgər mümkündürsə config validation et:

- nginx üçün: `nginx -t`
- docker-compose üçün: `docker compose config`

4. Dəyişiklikdən sonra xidmətin davranışını yoxla:

- servis status
- loglarda yeni error varmı
- health endpoint

## 7.10 dəyişiklikdən əvvəl backup almaq

Backup strategiyası sadə ola bilər:

- eyni qovluqda `.bak` fayl
- tarixli `.bak_YYYY-MM-DD_HHMM`

Vacib olan:

- backup-u tapmaq asan olsun
- rollback zamanı “hansı versiya?” sualına cavab olsun

---

Növbəti bölmə icazələr və istifadəçilər (chmod/chown/sudo/root) mövzusunu daha sistemli verir və “permission denied” xətasını analiz etməyi öyrədir.
