# Bölmə 5. Linux daxilində hərəkət və əsas komandalar

Bu bölmə Linux-da “tez və təhlükəsiz” işləmək üçün gündəlik komandaları sistemli izah edir.

## 5.1 `pwd`

“Print working directory” — hazırda hansı qovluqdasınız.

```bash
pwd
```

Triage zamanı faydalıdır: kiməsə “mən burdayam” demək üçün də.

## 5.2 `ls`, `ls -la`

Qovluq içini göstərir:

```bash
ls
ls -la
```

`-la` niyə vacibdir:

- gizli fayllar görünür (`.` ilə başlayanlar)
- permission/owner görünür

## 5.3 `cd`

Qovluq dəyişmək:

```bash
cd /opt
cd ~
cd -
```

- `cd ~` — home qovluğa qaytarır
- `cd -` — əvvəlki qovluğa qaytarır

## 5.4 `tree`

Qovluq strukturunu ağac kimi göstərir.

```bash
tree
```

Dərinliyi limitləmək:

```bash
tree -L 2
```

Əgər `tree` qurulu deyilsə, OS-dən asılı olaraq paket meneceri ilə quraşdırıla bilər (Bölmə 2.8).

## 5.5 `clear`

Terminal ekranını təmizləyir:

```bash
clear
```

Qısa yol: `Ctrl+L`.

## 5.6 `history`

Əvvəl işlədilən komandaları göstərir:

```bash
history
history | tail -n 20
```

Təhlükəsizlik qeydi:

- komandada secret yazmamaq daha doğrudur; `history`-də qala bilər.

## 5.7 Tab completion

Linux shell-də (bash/zsh) tab completion çox vaxt edir:

- qovluq/fayl adını avtomatik tamamlayır
- typo riskini azaldır

Misal:

- `cd /op` yazıb `Tab` basanda `/opt` tamamlaya bilər.

## 5.8 Wildcard istifadəsi

Wildcard (şablon) fayl seçməyə kömək edir:

- `*` — istənilən simvol ardıcıllığı
- `?` — tək simvol

Misal:

```bash
ls *.json
ls docker-compose*.yml
```

Risk:

- wildcard ilə `rm` işlətmək təhlükəlidir. Silməzdən əvvəl `ls` ilə yoxlayın.

## 5.9 Hidden file anlayışı

`.` ilə başlayan fayllar gizlidir.

Misal:

- `.env`
- `.bashrc`
- `.ssh/`

Görmək üçün:

```bash
ls -la
```

## 5.10 Path-lə rahat işləmək

BA üçün ən çox qarışan mövzular:

- absolute path vs relative path

### Absolute path

`/` ilə başlayır və harada olmağınızdan asılı deyil:

```bash
cat /etc/hosts
```

### Relative path

Hazırkı qovluğa görə işləyir:

```bash
cat ./appsettings.json
cat ../vault.settings.json
```

### “Current directory” (`.`) və “parent” (`..`)

- `.` → cari qovluq
- `..` → bir üst qovluq

Misal:

```bash
ls .
ls ..
```

---

Növbəti bölmələrdə fayllarla iş (`mkdir`, `cp`, `mv`, `rm`), find/grep ilə axtarış və config dəyişəndə nəzarət addımları daha geniş izah olunacaq.
