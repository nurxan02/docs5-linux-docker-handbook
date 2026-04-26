# Bölmə 2. Linux əsasları

Bu bölmə “serverdə it-bata düşməmək” üçün minimum Linux biliklərini verir. Məqsəd DevOps olmaq deyil; məqsəd təhlükəsiz şəkildə:

- qovluğu tapmaq,
- faylı oxumaq,
- log izləmək,
- permission problemini başa düşmək,
- read-only yoxlamaları etməkdir.

## 2.1 SSH ilə qoşulma

Linux serverlərə adətən SSH ilə qoşuluruq.

Ən tipik format:

```bash
ssh username@server_ip
```

Port standartdan fərqlidirsə:

```bash
ssh -p 2222 username@server_ip
```

Əgər jump/bastion istifadə olunursa, bu Bölmə 3-də detallı veriləcək.

## 2.2 Fayl sistemi

Linux-da fayl sistemi kökdən başlayır: `/`.

Ən çox rast gəlinən qovluqlar:

- `/home/<user>` — user-in “ev” qovluğu
- `/etc` — sistem konfiqləri
- `/var/log` — log faylları
- `/opt` — tətbiqlər üçün tez-tez istifadə olunan yer (Docs5 kimi deploy-larda çox rast gəlinir)
- `/tmp` — müvəqqəti fayllar

BA üçün əsas:

- nəyisə axtaranda əvvəlcə “tətbiq harada yerləşir?” sualını verin.
- prod-da təsadüfi qovluqlarda fayl yaratmaqdan çəkinin.

## 2.3 Navigation (pwd, ls, cd)

- `pwd` — harada olduğunuzu göstərir
- `ls` — qovluğun içini göstərir
- `cd` — qovluq dəyişir

Misal:

```bash
pwd
ls -la
cd /opt
```

`ls -la` xüsusilə faydalıdır, çünki:

- gizli faylları göstərir (`.` ilə başlayan)
- owner/permission göstərir

## 2.4 Fayl əməliyyatları (cp, mv, rm)

- `cp` — kopyalama
- `mv` — köçürmə/rename
- `rm` — silmə

Təhlükəsiz yanaşma:

- dəyişiklik etməzdən əvvəl backup kopya yaradın

Misal:

```bash
cp appsettings.json appsettings.json.bak
```

> Prod-da `rm -rf` “qırmızı bayraqdır”. Əmin deyilsinizsə, etməyin.

## 2.5 Fayl baxış və edit (cat, nano, tail)

Read-only baxış üçün ən çox istifadə edilənlər:

- `cat file` — faylı tam çıxarır (kiçik fayllar üçün)
- `less file` — böyük faylda rahat gəzmək (q- ilə çıxılır)
- `head -n 50 file` — ilk 50 sətr
- `tail -n 200 file` — son 200 sətr
- `tail -f file` — logu canlı izləmək

Edit üçün:

- `nano file` — sadə editor (Ctrl+O yaz, Ctrl+X çıx)

> BA üçün tövsiyə: prod-da edit etmək lazımdırsa, mümkün qədər dəyişiklikləri əvvəlcə test/pre-prod-da hazırlayıb, prod-da minimal risklə tətbiq etmək daha düzgündür.

## 2.6 Permissions (chmod, chown)

Linux-da permission 3 rola bölünür:

- owner (u)
- group (g)
- others (o)

və 3 icazə növü var:

- read (r)
- write (w)
- execute (x)

Qısa məna:

- `read (r)`:
  - faylda: məzmunu oxumağa icazə verir
  - qovluqda: içindəki adları görməyə icazə verir
- `write (w)`:
  - faylda: faylı dəyişməyə icazə verir
  - qovluqda: içində fayl yaratmaq/silmək/rename etməyə icazə verir
- `execute (x)`:
  - faylda: faylı proqram kimi işə salmağa icazə verir
  - qovluqda: qovluğa daxil olmağa (`cd`) və içində gəzməyə icazə verir

`ls -la` çıxışında misal:

- `-rw-r--r--` → fayl
- `drwxr-xr-x` → qovluq

Burada ilk simvol obyekt tipidir:

- `-` adi fayl
- `d` qovluq

Sonrakı 9 simvol 3-3 bölünür:

- `rw-` (owner)
- `r--` (group)
- `r--` (others)

Rəqəmsal (numeric) permission məntiqi:

- `r = 4`
- `w = 2`
- `x = 1`

Toplanaraq yazılır:

- `7 = rwx = 4+2+1`
- `6 = rw- = 4+2`
- `5 = r-x = 4+1`
- `4 = r-- = 4`

Tez-tez istifadə olunan permission kodları:

- `777` → `rwxrwxrwx`
  - owner, group, others hamısı oxu/yaz/execute edə bilir
  - praktikada çox risklidir, prod üçün tövsiyə edilmir
- `755` → `rwxr-xr-x`
  - owner tam səlahiyyətlidir, qalanlar oxu+execute
  - qovluqlar və executable script-lər üçün çox yayğındır
- `775` → `rwxrwxr-x`
  - owner və group tam səlahiyyətli, others oxu+execute
  - komanda ilə ortaq işlənən deploy qovluqlarında istifadə oluna bilər
- `700` → `rwx------`
  - yalnız owner daxil ola və dəyişə bilir
  - private qovluqlar və həssas fayllar üçün uyğundur
- `655` → `rw-r-xr-x`
  - owner oxu+yazır, group/others oxu+execute edir
  - adi config faylları üçün çox yayğın deyil, amma bəzi xüsusi hallarda görünə bilər
- `644` → `rw-r--r--`
  - owner oxu+yazı, qalanlar yalnız oxu
  - config və text faylları üçün ən çox istifadə olunan seçimdir
- `600` → `rw-------`
  - yalnız owner oxu+yazı edə bilir
  - secret tipli fayllar üçün (məs: private key) standart seçimdir

Tipik vəziyyət:

- config faylları: `644` (`rw-r--r--`)
- qovluqlar: `755` (`rwxr-xr-x`)

`chmod` icazəni dəyişir. Məsələn:

```bash
chmod 644 appsettings.json
```

Yəni:

- owner oxuya və yaza bilir
- group və others yalnız oxuya bilir

Faylı icra edilən script etmək üçün:

```bash
chmod +x deploy.sh
```

Bu, owner/group/others üçün execute bitini əlavə edir. Daha dəqiq vermək istəsəniz:

```bash
chmod u+x deploy.sh
```

Yalnız owner üçün `x` əlavə edir.

Praktik komandalar:

```bash
# Cari permission-a bax
ls -l appsettings.json

# Simvolik formatla icazə ver
chmod u=rw,g=r,o=r appsettings.json

# Qovluq və altındakı hər şeyə tətbiq et (ehtiyatla)
chmod -R 755 /opt/myapp/scripts
```

`chown` owner-i dəyişir:

```bash
sudo chown root:root appsettings.json
```

`owner:group` hissəsində ad əvəzinə rəqəm də ola bilər.

- `1000:1000` kimi yazılış `UID:GID` deməkdir
- birinci `1000` istifadəçinin ID-si (UID)
- ikinci `1000` qrupun ID-si (GID)

Məsələn:

```bash
sudo chown 1000:1000 appsettings.json
```

Bu komanda faylın sahibini UID `1000`, qrupunu GID `1000` edir.

Yoxlama komandaları:

```bash
# Fayl sahibinin adını və qrupunu göstərir
ls -l appsettings.json

# Fayl sahibinin UID/GID rəqəmlərini göstərir
ls -ln appsettings.json

# Cari istifadəçinin UID/GID məlumatı
id
```

Format:

```bash
sudo chown <owner>:<group> <fayl_veya_qovluq>
```

Məsələn, tətbiq fayllarını `docs5` istifadəçisi və qrupu ilə əlaqələndirmək:

```bash
sudo chown -R docs5:docs5 /opt/docs5
```

Yalnız group dəyişmək üçün `chgrp`:

```bash
sudo chgrp nginx /opt/docs5/logs
```

Yoxlama üçün:

```bash
ls -la /opt/docs5
id
groups
```

> “777 ver, keçsin” yanaşması təhlükəlidir. İcazəni həmişə minimum ehtiyac prinsipi ilə verin.

`777` (`rwxrwxrwx`) niyə risklidir:

- hər kəs yazıb dəyişə bilər
- təsadüfi və ya zərərli dəyişiklik ehtimalı artır
- audit və təhlükəsizlik intizamı pozulur

BA üçün qayda:

- əvvəl `ls -la` ilə real vəziyyəti yoxlayın
- sonra ən dar icazəni verin
- dəyişiklikdən sonra yenidən `ls -la` ilə təsdiq edin

## 2.7 sudo və root

- `sudo` bir əməliyyatı admin səlahiyyəti ilə icra edir.
- `root` sistemin ən yüksək səlahiyyətli istifadəçisidir.

Prod intizamı:

- root ilə uzun müddət işləməyin.
- `sudo` istifadə edəndə “həqiqətən lazımdır?” sualını verin.

## 2.8 apt və dnf paket menecerləri ilə işləmək

Serverin OS-nə görə fərqlənir:

- Ubuntu/Debian → `apt`
- RHEL/CentOS/Fedora → `dnf` (və ya köhnə `yum`)

Məqsəd: paket qurmaq, update etmək.

Ubuntu nümunəsi:

```bash
sudo apt update
sudo apt install -y htop
```

RHEL/Fedora nümunəsi:

```bash
sudo dnf install -y htop
```

> Prod-da paket install/update dəyişiklik sayılır. Əgər prosedur tələb olunursa, change request olmadan etməyin.

---

Növbəti bölmə SSH bağlantısını daha detallı izah edəcək: password vs key, jump server, tipik xətalar və təhlükəsiz sessiya davranışı.
