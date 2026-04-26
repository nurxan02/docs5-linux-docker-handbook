# Bölmə 21. Docs5 qovluq və servis strukturu

Bu bölmə Docs5-in serverdə “harada yaşadığını” anlamaq üçündür. Incident zamanı ən çox vaxt itirən şey: doğru qovluğu, doğru compose faylını, doğru log yerini tapa bilməməkdir.

> Qeyd: Real yerləşim təşkilata görə dəyişə bilər. Burada ən çox rast gəlinən “/opt/wss/…” modelini izah edirik.

## 21.1 `/opt/wss/docs`

Bu qovluq çox vaxt Docs5 tətbiqinin (və ya onun deploy artefaktlarının) yerləşdiyi əsas qovluq olur.

Burada ola biləcək şeylər:

- `docker-compose.yml` və ya bir neçə compose faylı
- `.env` və ya environment faylları
- `appsettings.json` və əlavə config fayllar
- upgrade/migration util-lərinin qovluqları

BA üçün praktik:

- Əgər “servis qaldırmaq” lazımdırsa, əvvəlcə compose faylının harada olduğunu tapın.
- Əgər “son dəyişiklik nə idi?” sualı varsa, bu qovluqda timestamp-lərə (`ls -la`) baxmaq faydalıdır.

## 21.2 `/opt/wss/storage`

Storage servisi və ya storage data çox vaxt ayrıca yerdə saxlanılır.

Burada ola bilər:

- storage servis config-i
- storage volume mount-ları
- blob faylları (siyasətə görə)

Təhlükəsizlik:

- Storage qovluğunda “təsadüfi silmə” böyük data itkisi yarada bilər.
- BA üçün düzgün addım: ölçü yoxlama (`du -sh`) + eskalasiya.

## 21.3 `/opt/wss/vault`

Əgər Vault ayrıca servis kimi deploy olunubsa, onun data/config yerləri burada ola bilər.

BA üçün:

- Vault down olanda bir çox servis “config tapa bilmir” kimi görünə bilər.

## 21.4 `/opt/wss/keycloak`

Keycloak yerləşimi:

- Keycloak container + data volume
- realm export/import faylları (bəzən)

## 21.5 `/opt/wss/postgres` və ya system PostgreSQL

PostgreSQL iki modeldə olur:

1. Docker container içində
2. Host OS-də system service kimi (`systemctl` ilə)

BA üçün fərq:

- container modeldə: `docker ps`, `docker logs`
- system modeldə: `systemctl status postgresql`, `journalctl -u postgresql`

## 21.6 nginx config yerləri

Nginx adətən:

- host-da quraşdırılırsa: `/etc/nginx/`
- container-dədirsə: compose volume-ları vasitəsilə hostdan mount olunur

Tipik:

- `/etc/nginx/nginx.conf`
- `/etc/nginx/conf.d/*.conf`
- `/etc/nginx/sites-enabled/*` (Ubuntu-da)

## 21.7 env və `vault.settings` faylları

Docs5-in çox hissəsində “real secret” config-lar:

- `.env` (compose üçün)
- `vault.settings.json` (application üçün)

Bu repodakı util-lər bunu göstərir:

- `appsettings.json` içində `"PrependConfigFiles": ["vault.settings.json"]`
- compose-da `../../vault.settings.json:/app/vault.settings.json:ro` mount edilir

BA üçün vacib qayda:

- `vault.settings.json` mətnini chat/email ilə paylaşmayın.
- log və screenshot-larda credential görünməsin.

## 21.8 hansı servis hansı domainə cavab verir

Əsas sual:

- `docs.company.az` hansı servisin upstream-idir?

Cavab adətən Nginx config-dədir:

- `server_name docs.company.az;`
- `proxy_pass http://...;`

---

Növbəti bölmə Nginx reverse proxy və ən tipik 502 problemlərinin analizinə keçəcək.
