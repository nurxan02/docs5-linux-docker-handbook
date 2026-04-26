# Bölmə 4. WinSCP ilə SFTP/SSH bağlantısı

WinSCP Windows istifadəçiləri üçün serverə fayl atmaq/çəkmək və bəzən uzaq fayla baxmaq üçün çox istifadə olunan alətdir.

> Qeyd: Prod mühitdə “fayl yüklədim” əməliyyatı da dəyişiklik sayılır. Xüsusən config fayllarında overwrite riski var.

## 4.1 WinSCP nədir

WinSCP:

- SFTP/SCP ilə fayl transfer edir
- remote faylları görməyə və (icazə varsa) edit etməyə imkan verir
- session-ları yadda saxlayır

## 4.2 SFTP və SCP fərqi

- **SFTP** (SSH File Transfer Protocol) — SSH üstündə fayl transfer protokolu, daha geniş funksionallıq.
- **SCP** — daha sadə “copy” mexanizmi.

Praktikada:

- çox vaxt SFTP seçilir
- təşkilatın siyasətinə görə məhdudiyyət ola bilər

## 4.3 WinSCP ilə yeni connection yaratmaq

1. WinSCP açın
2. `New Site` seçin
3. File protocol: adətən `SFTP`
4. Host name, Port number, User name daxil edin

## 4.4 Hostname, port, username, password doldurulması

Tipik:

- Host name: `jump.company.az` və ya `10.0.10.25`
- Port: `22` (və ya sizə verilən port)
- User name: `ba`, `appuser` və s.

Password:

- bir dəfəlik daxil etmək mümkündür
- amma təhlükəsizlik üçün key daha üstündür

## 4.5 SSH key ilə WinSCP bağlantısı

Əgər sizdə private key varsa:

- WinSCP-də `Advanced...` → `SSH` → `Authentication`
- `Private key file` seçin

WinSCP bəzən `.ppk` format istəyir (PuTTY key). OpenSSH formatını çevirmək lazım ola bilər.

## 4.6 Saved session yaratmaq

Eyni serverə tez-tez girirsinizsə:

- `Save` edin
- session adı verin: `prod-app (via jump)` kimi

Prod üçün tövsiyə:

- session adında mühiti yazın (test/pre-prod/prod)
- səhv mühitə upload riskini azaldır

## 4.7 Server fayl sistemində gəzmək

WinSCP iki panel göstərir:

- sol: lokal kompüter
- sağ: server

Serverdə tipik qovluqlar:

- `/opt/...` (tətbiq yerləşimi)
- `/var/log/...` (loglar)
- `/etc/...` (nginx config və s.)

## 4.8 Windows-dan Linux-a fayl upload etmək

Addımlar:

1. sağ paneldə hədəf qovluğa keçin
2. sol paneldən faylı seçin
3. drag-and-drop və ya `Upload`

Risk:

- eyni adlı fayl varsa overwrite edə bilərsiniz

Təhlükəsiz yanaşma:

- əvvəl serverdə `.bak` backup yaradın
- upload edəndə “Overwrite” etməzdən əvvəl iki dəfə yoxlayın

## 4.9 Linux-dan Windows-a fayl download etmək

Bu, log və ya backup faylları üçün lazımlı ola bilər:

1. serverdən faylı seçin
2. lokal qovluğa sürükləyin

Tövsiyə:

- böyük fayllarda (GB) download vaxt aparır
- əvvəl `zip/tar.gz` edib ölçünü azaldmaq olar

## 4.10 Drag-and-drop və manual copy

- Drag-and-drop rahatdır, amma “yanlış qovluğa atdım” riski var.
- Manual copy (Copy → Paste) daha nəzarətlidir.

Prod-da tövsiyə:

- dəyişiklik edərkən manual və daha kontrollu üsul seçin.

## 4.11 Permissions və overwrite riskləri

Əgər upload etdiyiniz faylın owner/permission-ı dəyişirsə:

- servis həmin faylı oxuya bilməyə bilər
- `permission denied` kimi error çıxar

BA üçün praktik:

- “niyə servis açılmır?” sualında son dəyişikliklərə baxın: config faylı upload olunub?

## 4.12 WinSCP ilə uzaq faylı edit etmək

WinSCP remote faylı edit edəndə adətən belə edir:

- faylı lokala çəkir
- edit edir
- geri yükləyir

Risklər:

- concurrency (siz edit edərkən başqa biri də dəyişir)
- backup olmadan dəyişiklik

Prod-da minimal intizam:

- edit etməzdən əvvəl backup
- dəyişiklikləri kiçik saxlayın
- dəyişiklikdən sonra config test varsa, icra edin (məsələn, nginx config test)

## 4.13 WinSCP-də tipik xətalar və həllər

- `Network error: Connection timed out` → VPN/jump/firewall
- `Authentication failed` → username/password/key problem
- `Permission denied` (upload zamanı) → hədəf qovluğa yazmaq icazəniz yoxdur
- `Host key has changed` → host key verification; təsdiq olmadan davam etməyin

---

Növbəti bölmə Linux daxilində gündəlik komandaları daha sistemli şəkildə izah edir: `pwd`, `ls`, `cd`, `tree`, `history`, wildcard və s.
