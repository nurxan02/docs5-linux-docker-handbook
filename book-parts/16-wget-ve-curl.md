# Bölmə 16. `wget` və `curl` ilə fayl və endpoint işi

`wget` və `curl` BA üçün “API işləyir?” və “bu URL-dən fayl endirə bilərəm?” suallarına cavab verən əsas alətlərdir.

## 16.1 `wget` nədir

- əsasən fayl endirmək üçün
- HTTP/HTTPS üstündən işləyir

## 16.2 `curl` və `wget` fərqi

- `curl` daha çox “request yoxlama” (header, status code, auth)
- `wget` daha çox “download”

Amma ikisi də çox şey edə bilir.

## 16.3 URL-dən fayl endirmək

```bash
wget https://example.com/file.tar.gz
```

## 16.4 output file təyin etmək

```bash
wget -O backup.tar.gz https://example.com/download?id=123
```

## 16.5 self-signed / internal certificate halları

Internal TLS-də bəzən sertifikat trust olunmur.

`curl` ilə:

```bash
curl -vk https://internal.service/health
```

- `-k` certificate verify-ni keçər (yalnız test/diagnostic üçün)

> Prod təhlükəsizliyi: `-k` “normal işləmə” üçün yox, yalnız diagnoz üçündür.

## 16.6 auth tələb edən endpoint-lər

Header ilə token:

```bash
curl -H "Authorization: Bearer <TOKEN>" https://docs.company.az/api/me
```

X-API-KEY nümunəsi:

```bash
curl -H "X-API-KEY: <KEY>" https://docs.company.az/api/ping
```

Secret-ları komanda sətrində yazmaq risklidir (history). Mümkün olduqda dəyişənlə:

```bash
export API_KEY="..."
curl -H "X-API-KEY: $API_KEY" https://...
```

## 16.7 connection troubleshoot

Tipik yoxlamalar:

- DNS?

```bash
getent hosts docs.company.az
```

- Port açıqdır?

```bash
ss -tuln | grep 443
```

- HTTP status nədir?

```bash
curl -I https://docs.company.az
```

- Proxy arxasında upstream problemi varmı?

```bash
curl -v https://docs.company.az
```

---

Növbəti bölmələr Docker-a keçir: image/container/volume/network anlayışları, `docker ps`, loglar və Compose əməliyyatları.
