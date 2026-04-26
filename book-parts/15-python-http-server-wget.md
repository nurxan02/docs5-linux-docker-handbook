# Bölmə 15. Müvəqqəti fayl paylaşımı: Python HTTP server + `wget`

Bəzən `scp` ilə problem olur (jump, key, policy) və sizə qısa müddətlik “serverdə qovluğu HTTP ilə paylaşım, digər serverdən çəkim” yanaşması lazım olur.

> Təhlükəsizlik xəbərdarlığı: HTTP server açmaq “paylaşım” deməkdir. Prod-da bunu yalnız daxili şəbəkədə, minimal vaxt və firewall nəzarəti ilə edin.

## 15.1 Bu yanaşma nə vaxt lazımdır

- eyni subnetdə iki server arasında sürətli transfer
- `scp` policy ilə bloklanıb
- müvəqqəti log/artefakt paylaşmaq lazımdır

## 15.2 Python ilə qovluğu host etmək

Qovluğa keçin:

```bash
cd /tmp/debug
```

Python 3 ilə server açın:

```bash
python3 -m http.server 8080
```

Bu, həmin qovluğu `http://<server-ip>:8080/` ilə paylaşır.

## 15.3 hansı portda publish etmək

- 8080 çox vaxt rahatdır
- amma təşkilatın policy-si və firewall qaydaları nəzərə alınmalıdır

## 15.4 digər serverdən `wget` ilə çəkmək

Digər serverdə:

```bash
wget http://10.0.10.25:8080/error.log
```

## 15.5 lokal IP ilə fayl ötürmə ssenarisi

Ən təhlükəsiz:

- yalnız private IP
- yalnız daxili subnet
- mümkün olduqda source IP məhdudiyyəti

## 15.6 firewall və port yoxlaması

Host tərəfdə port dinləyirmi?

```bash
ss -tuln | grep 8080
```

Firewall açıqdır?

```bash
sudo ufw status
```

## 15.7 directory listing riskləri

Python http server default olaraq directory listing verir. Bu riskdir.

Minimal tədbirlər:

- `/tmp` kimi müvəqqəti qovluq istifadə edin
- yalnız lazım olan faylları qoyun
- iş bitən kimi serveri bağlayın

## 15.8 iş bitəndən sonra host-un bağlanması

Terminalda `Ctrl+C`.

## 15.9 `wget` ilə tək fayl çəkmək

```bash
wget -O nginx_error.log http://10.0.10.25:8080/error.log
```

## 15.10 `wget` ilə recursive çəkmə əsasları

Recursive risklidir (çox fayl çəkə bilər):

```bash
wget -r -np -nH --cut-dirs=0 http://10.0.10.25:8080/
```

> Prod-da recursive istifadə etməzdən əvvəl məzmunu dəqiqləşdirin.

---

Növbəti bölmə `wget` və `curl` ilə endpoint yoxlama və fayl endirmə mövzularını genişləndirir.
