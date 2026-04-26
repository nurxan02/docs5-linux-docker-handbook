# Bölmə 25. Postman ilə API yoxlamaları

API problemi olanda BA üçün ən güclü bacarıq: UI-dan asılı olmadan API-nin cavab verib-vermədiyini yoxlamaqdır.

## 25.1 Postman nədir

Postman:

- HTTP request göndərmək
- response-u (status, headers, body) oxumaq
- auth header-ləri idarə etmək
- collection və environment-lər ilə standart test ssenariləri yaratmaq

## 25.2 GET request atmaq

Sadə test:

- Method: `GET`
- URL: `https://docs.company.az/api/health` (placeholder)

Expected:

- `200 OK` və sadə JSON/text

## 25.3 POST request atmaq

POST adətən body tələb edir.

- Method: `POST`
- Body: JSON

## 25.4 Headers əlavə etmək

Tipik header-lər:

- `Content-Type: application/json`
- `Authorization: Bearer <token>`
- `X-API-KEY: <key>`

## 25.5 X-API-KEY nədir

Bəzi internal API-lər Keycloak token əvəzinə `X-API-KEY` tələb edə bilər.

Təhlükəsizlik:

- API key secret-dir, paylaşılmır.

## 25.6 X-API-KEY ilə request

Postman-də:

- Headers tab → `X-API-KEY` əlavə edin

CLI alternativi (curl):

```bash
curl -H "X-API-KEY: <KEY>" https://docs.company.az/api/ping
```

## 25.7 Authorization header anlayışı

Keycloak tipik “Bearer token” verir:

- `Authorization: Bearer eyJ...`

Token müddəti bitərsə:

- `401 Unauthorized` gələ bilər

## 25.8 query param və path param

- Query param: `?page=1&size=50`
- Path param: `/api/items/123`

Postman-də:

- Params tab → query param-ları əlavə etmək daha rahatdır.

## 25.9 body göndərmək

JSON body nümunəsi:

```json
{ "from": "2026-01-01", "to": "2026-01-31" }
```

`form-data` adətən file upload üçün istifadə olunur.

## 25.10 response oxumaq

3 əsas hissə:

- status code (200/400/401/403/404/500)
- headers (server, content-type, request-id)
- body (error mesajı, data)

BA üçün:

- `500` → server-side error (log lazımdır)
- `401/403` → auth/permission problem
- `404` → route səhv, proxy səhv, və ya feature disabled

## 25.11 SSL verify problemləri

Internal sertifikatlarda Postman “certificate not trusted” göstərə bilər.

Yanaşma:

- mümkün olduqda root certificate-i sistemə əlavə etmək (Security/DevOps ilə)
- “Disable SSL verification” yalnız diaqnostika üçün, prod intizamı ilə

## 25.12 internal API endpoint-lərin test edilməsi

Minimal “ping/health” endpoint-ləri saxlayın:

- `/health`
- `/api/ping`

Bu endpoint-lər incident zamanı “servis yaşayır” cavabı verir.

## 25.13 curl-dan Postman-a və Postman-dan curl-a keçid

Postman-də:

- “Code” düyməsi ilə curl export etmək olur

Bu çox faydalıdır:

- DevOps/Dev ilə eyni request-i paylaşmaq
- incident qeydində request-i sənədləşdirmək

---

Növbəti bölmə auth: Vault/Keycloak axını, servislər down olanda nə baş verir və BA-nın nə qədər bilməli olduğu.
