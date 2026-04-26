# Bölmə 19. Container daxilində işləmək

Bəzən log və config hostda deyil, konteynerin içində olur. Bu bölmədə konteynerə daxil olub read-only yoxlama etməyi öyrənirsiniz.

## 19.1 `docker exec -it`

Konteynerə shell açmaq:

```bash
docker exec -it <container_name> bash
```

Əgər `bash` yoxdursa:

```bash
docker exec -it <container_name> sh
```

## 19.2 `bash` və `sh` fərqi

- `bash` daha funksional shell
- `sh` daha sadə, minimal image-lərdə olur

## 19.3 container daxilində fayl tapmaq

Container içində:

```bash
find / -name "appsettings.json" 2>/dev/null | head
```

## 19.4 container daxilində config oxumaq

```bash
cat /app/appsettings.json
```

Əgər config volume ilə mount olunubsa, hostda da ola bilər.

## 19.5 container daxilində log faylı axtarmaq

```bash
find /var/log -type f 2>/dev/null | head
```

Amma çox servis logu stdout-a yazır — o zaman `docker logs` daha doğru yerdir.

## 19.6 container daxilində command işlətmək

Shell açmadan da icra etmək olar:

```bash
docker exec <container_name> ls -la /app
```

## 19.7 container içərisinə fayl yerləşdirmək (`docker cp`)

Hostdan konteynerə:

```bash
docker cp ./appsettings.json <container_name>:/app/appsettings.json
```

> Prod-da bu adətən tövsiyə olunmur, çünki container içində dəyişiklik “persistent” olmaya bilər. Daha doğru yol: volume mount + controlled deploy.

## 19.8 container-dən host-a fayl çıxarmaq

```bash
docker cp <container_name>:/app/appsettings.json ./appsettings.json
```

Bu, audit/troubleshooting üçün faydalıdır.

## 19.9 container daxilində müvəqqəti debug

Debug edərkən:

- `env` ilə environment variable-ləri yoxlamaq
- `cat` ilə config oxumaq

Amma:

- prod-da əlavə paket install etmək risklidir
- mümkün olduqda host tərəfdə yoxlama edin

## 19.10 dəyişikliklərin persistent olub-olmaması

Əsas prinsip:

- container içində etdiyiniz dəyişiklik image-də deyil, konteynerin “üst layer”-ində qalır
- konteyner silinib yenidən yaradılarsa, dəyişiklik itə bilər

Ona görə prod-da:

- dəyişikliklər compose/deploy yolu ilə edilməlidir

---

Növbəti bölmə container logları və troubleshooting: `docker logs`, restart loop, exit code, “Restarting (139)” kimi halların mənası və root cause tapmaq.
