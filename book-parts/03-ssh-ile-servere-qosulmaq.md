# Bölmə 3. SSH ilə serverə qoşulmaq

SSH (Secure Shell) Linux serverə təhlükəsiz (şifrəli) qoşulmaq üçün standart üsuldur.

## 3.1 SSH nədir

SSH sizə uzaq serverdə terminal açmağa imkan verir:

- komandaları sanki serverin yanında oturmuş kimi icra edirsiniz
- trafik şifrələnir

## 3.2 Login/password ilə qoşulma

Format:

```bash
ssh username@hostname
```

Qoşulanda sizdən password istəyə bilər.

Prod tövsiyəsi:

- password-u heç vaxt chat-a yazmayın
- password manager istifadə edin
- mümkün olduqda SSH key-ə keçin

## 3.3 SSH key ilə qoşulma

SSH key cütlükdür:

- private key — sizdə olur, paylaşılmır
- public key — serverdə authorized_keys-ə əlavə olunur

Qoşulma:

```bash
ssh -i ~/.ssh/id_rsa username@hostname
```

Əgər key parol ilə qorunursa, passphrase soruşacaq.

## 3.4 Jump server üzərindən qoşulma

Bəzən prod serverlər birbaşa internetdən açıq olmur. Bu zaman əvvəlcə “jump/bastion” serverə girib, ordan hədəf serverə keçirsiniz.

Sadə iki addım:

```bash
ssh ba@jump.company.az
ssh appuser@10.0.10.25
```

Daha rahat “ProxyJump” ilə (lokal `~/.ssh/config`):

```sshconfig
Host prod-app
  HostName 10.0.10.25
  User appuser
  ProxyJump ba@jump.company.az
```

Sonra:

```bash
ssh prod-app
```

## 3.5 Hostname və IP ilə qoşulma fərqi

- **IP** sabit ola bilər, amma bəzən dəyişir.
- **Hostname** DNS ilə IP-yə çevrilir.

Troubleshooting zamanı:

- hostname işləmirsə, IP ilə yoxlayın
- DNS problemindən şübhələnirsinizsə, `nslookup`/`dig` (əgər icazə varsa) istifadə olunur

## 3.6 Qoşulma zamanı tipik xətalar

Bu hissə BA üçün ən vacib hissələrdəndir: xəta mətnini oxuyub ilk səbəbi təxmin etmək.

### `connection refused`

Mənası: serverə çatdınız, amma həmin portda SSH servisi cavab vermir.

Tipik səbəblər:

- yanlış port
- sshd down
- firewall portu bağlayıb

Nə yoxlamaq olar:

- portu dəqiqləşdirin (`-p` istifadə edin)
- eyni hosta ping/curl mümkünmü (şəbəkə siyasətindən asılıdır)

### `permission denied (publickey,password)`

Mənası: server sizi qəbul etmir.

Tipik səbəblər:

- username səhv
- password səhv
- key yanlış seçilib
- sizin user-in serverdə icazəsi yoxdur

Nə etmək:

- username-i dəqiqləşdirin
- doğru key faylını seçin (`-i`)
- DevOps/Security-dən user icazəsini yoxlatdırın

### `no route to host`

Mənası: şəbəkə səviyyəsində serverə yol yoxdur.

Tipik səbəblər:

- VPN qoşulu deyil
- subnet arası routing yoxdur
- firewall şəbəkə səviyyəsində bloklayır

Nə etmək:

- VPN statusunu yoxlayın
- jump server tələb olunurmu, dəqiqləşdirin

### `host key verification failed`

Mənası: SSH serverin “fingerprint”i dəyişib və sizdəki `known_hosts` ilə uyğun gəlmir.

Səbəb ola bilər:

- server yenidən qurulub
- IP başqa serverə keçib
- (nadir) man-in-the-middle risk

Tövsiyə:

- Prod-da bu xətada dərhal “sil keçsin” etməyin.
- DevOps-dan yeni fingerprint təsdiqi alın.

## 3.7 SSH session təhlükəsizliyi

BA üçün praktiki qaydalar:

- eyni anda çoxlu serverdə root sessiya açmayın
- əməliyyatları “copy-paste” edəndə iki dəfə yoxlayın
- `history`-də secret qalmasın deyə:
  - secret-ları komanda sətrində yazmamaq daha yaxşıdır
  - mümkün olduqda environment/secret file istifadə olunur

## 3.8 Session-dan düzgün çıxmaq

- sadə çıxış: `exit`
- qısa yol: `Ctrl+D`

Əgər sessiya “donubsa”:

- `~.` (tilda + nöqtə) SSH bağlantısını kəsə bilər

---

Növbəti bölmə WinSCP ilə fayl ötürməni və uzaq fayl redaktəsini izah edir.
