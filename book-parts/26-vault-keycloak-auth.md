# Bölmə 26. Vault, Keycloak və auth əsasları

Auth problemi olanda simptomlar çox müxtəlif görünür: login olmur, token invalid, API 401/403 verir, UI redirect loop-a düşür. Bu bölmə BA üçün “minimum mental model” verir.

## 26.1 Docs5-də auth axını

Sadə model:

1. User UI-a girir
2. UI user-i Keycloak login-ə yönləndirir
3. User login edir
4. Keycloak token verir
5. UI/API token ilə request-lər edir

Problem harada ola bilər?

- Keycloak down
- Nginx redirect/proxy səhv
- clock/time drift (token time)
- client/realm config dəyişib

## 26.2 Keycloak nə üçündür

Keycloak:

- istifadəçi identifikasiyası
- rol/permission
- token issuance

BA üçün:

- realm, client, redirect URL anlayışlarını başa düşmək kifayətdir.

## 26.3 Vault nə üçündür

Vault və ya “vault settings” konsepti:

- secret-ları təhlükəsiz saxlamaq
- tətbiqlərə runtime-da secret təqdim etmək

Bu repo util-lərində “vault.settings.json” mount edilməsi bunu göstərir.

## 26.4 BA nə qədər bilməlidir

BA üçün məqsəd:

- auth simptomunu düzgün təsvir etmək
- 401 vs 403 fərqini anlamaq
- hansı komponentin down ola biləcəyini təxmin etmək

BA-nın etməməli olduğu:

- secret-ları oxumaq/paylaşmaq
- realm/client konfiqini tək dəyişmək

## 26.5 connection problemində ilkin yoxlama

Auth problemi gələndə:

- Keycloak URL açılır?
- Nginx 502 verir?
- API “health” işləyir?

CLI yoxlama:

```bash
curl -I https://keycloak.company.az
curl -I https://docs.company.az
```

## 26.6 domain, port, TLS, credentials əlaqəsi

Auth-da 4 qat:

- domain/DNS
- TLS (sertifikat)
- proxy (Nginx)
- credential/token

Səhv qat tapılmadan “password səhvdir” demək tez-tez yanlış olur.

## 26.7 bu servislər down olanda nə baş verir

- Keycloak down → login olmur, API 401 artar
- Vault/settings yoxdur → app start olmaya bilər və ya DB connection qırılar

BA üçün:

- loglarda `cannot load configuration` / `vault` / `secret` kimi sözlərə baxın.

---

Növbəti bölmə Storage əməliyyat məntiqi: blob/metadata fərqi, storage servis check, preview/file access problemləri.
