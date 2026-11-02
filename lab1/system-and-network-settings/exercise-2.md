# LAB-1: CLI (Komut Satırı Arayüzü) ile Çalışma

## 1. Amaç

Bu laboratuvar çalışmasında FortiGate cihazına CLI (Command Line Interface) üzerinden erişilecek ve komut satırının temel kullanım özellikleri öğrenilecektir.

Bu çalışma kapsamında:

- FortiGate CLI arayüzüne giriş yapılacaktır.
- Sistem durum bilgileri görüntülenecektir.
- Komut yardım mekanizması incelenecektir.
- Komut geçmişi ve klavye kısayolları kullanılacaktır.
- Otomatik komut tamamlama özelliği öğrenilecektir.
- Ağ arayüzü yapılandırmaları CLI üzerinden görüntülenecektir.

---

## 2. CLI Arayüzünün Keşfedilmesi

Bu bölümde FortiGate CLI arayüzünün temel özellikleri incelenecektir.

### 2.1. FortiGate CLI Üzerinde Oturum Açma

1. Local-FortiGate CLI arayüzünü açın.
2. Kullanıcı adı istendiğinde aşağıdaki bilgiyi girin:

```text
admin
```

3. Password alanına aşağıdaki şifreyi girin:

```text
password
```

4. `Enter` tuşuna basarak oturum açın.

### 2.2. Sistem Durum Bilgilerinin Görüntülenmesi

CLI üzerinde aşağıdaki komutu çalıştırın:

```bash
get system status
```

Bu komut, FortiGate cihazının temel sistem durum bilgilerini görüntüler.

Komut çıktısında aşağıdaki bilgiler yer alır:

- FortiGate seri numarası (Serial Number)
- Çalışma modu (Operation Mode)
- FortiOS sürümü
- Sistem çalışma süresi (Uptime)
- Donanım bilgileri
- Diğer sistem durum bilgileri

#### More Ekranının Kullanılması

Komut çıktısı ekran boyutunu aştığında CLI üzerinde `More` ifadesi görüntülenebilir.

Bu durumda aşağıdaki tuşlar kullanılabilir:

| İşlem | Kullanılacak Tuş |
|---|---|
| Çıktıyı sayfa sayfa ilerletmek | `Space` |
| Bir satır aşağı ilerlemek | `Enter` |
| Çıktı ekranından çıkmak | `q` |

---

### 2.3. Komut Yardım Mekanizmasının Kullanılması

Aşağıdaki komutu girin:

```bash
get ?
```

Bu komut, `get` komutundan sonra kullanılabilecek tüm seçenekleri listeler.

**Önemli Not:**

CLI üzerinde `?` karakterine bastığınızda bu karakter ekranda görüntülenmeyebilir. Bunun yerine FortiGate, ilgili komut için kullanılabilecek seçenekleri gösterir.

Bir yapılandırma seçeneğini tamamen belirtmek için komuttan sonra ek kelimeler girmeniz gerekebilir.

### 2.4. Önceki Komutların Görüntülenmesi

1. Klavyeden yukarı ok (`↑`) tuşuna basın.
2. Bir önceki komutun tekrar görüntülendiğini gözlemleyin.

Bu işlem sonucunda daha önce çalıştırdığınız aşağıdaki komut tekrar ekrana gelir:

```bash
get system status
```

CLI, daha önce çalıştırılan komutları geçmişte tutar. Bu sayede aynı komutları tekrar yazmak zorunda kalmazsınız.

---

## 3. CLI Klavye Kısayolları

FortiGate CLI, komut girişini hızlandırmak ve düzenlemek için çeşitli klavye kısayollarını destekler.

Aşağıdaki kısayolları deneyin:

| İşlem | Klavye Kısayolu |
|---|---|
| Önceki komutu görüntüleme | `↑` |
| Sonraki komutu görüntüleme | `↓` |
| Satırın başına gitme | `Ctrl + A` |
| Satırın sonuna gitme | `Ctrl + E` |
| Bir kelime geriye gitme | `Ctrl + B` |
| Bir kelime ileri gitme | `Ctrl + F` |
| İmlecin bulunduğu karakteri silme | `Ctrl + D` |
| Ekranı temizleme | `Ctrl + L` |
| Komutu iptal etme veya işlemden çıkma | `Ctrl + C` |
| Komut geçmişinde gezinme | `Ctrl + P` |

**Not:** `Ctrl + P` önceki komut geçmişi kaydına gitmek için kullanılır. Komut geçmişinde aşağı ve yukarı gezinmek için ok tuşları da kullanılabilir.

---

## 4. execute Komutunun İncelenmesi

FortiGate CLI üzerinde `execute` komutundan sonra kullanılabilecek seçenekleri görmek için aşağıdaki komutu girin:

```bash
execute ?
```

Bu komut, `execute` komutunun kabul ettiği seçenekleri listeler.

### 4.1. Otomatik Komut Tamamlama (Tab Completion)

FortiGate CLI, komutları otomatik tamamlama özelliğine sahiptir.

Bu özelliği denemek için:

1. Yeni bir satıra aşağıdaki ifadeyi yazın:

```bash
exe
```

2. Klavyeden `Tab` tuşuna basın.

CLI, yazdığınız ifadeyi tamamlayarak uygun komutu görüntüler.

Örneğin:

```bash
exe
```

`Tab` tuşuna basıldığında:

```bash
execute
```

şeklinde tamamlanır.

### 4.2. Tab Tuşuyla Komut Seçeneklerini Listeleme

1. `execute` komutundan sonra boşluk bırakın.
2. `Tab` tuşuna üç kez basın.
3. Her basışta ikinci kelimenin `execute` komutu için sıradaki olası seçenekle değiştiğini gözlemleyin.

Seçenekler alfabetik sırayla görüntülenir.

Bu özellik, kullanılabilir komutları keşfetmeyi kolaylaştırır.

### 4.3. Komutların Kısaltılarak Kullanılması

FortiGate CLI üzerinde birçok komut kısaltılarak kullanılabilir.

Örneğin:

| Tam Komut | Kısaltılmış Komut |
|---|---|
| `execute` | `exe` |
| `get` | `get` |
| `show` | `sho` |

Komutların kısaltılması, daha az tuşa basarak işlemlerin gerçekleştirilmesini sağlar.

**Dikkat:**

Aynı karakterlerle başlayan birden fazla komut varsa, kısaltma yeterince ayırt edici olmalıdır.

Örneğin bir kısaltma birden fazla komutla eşleşiyorsa FortiGate aşağıdaki gibi belirsiz komut hatası verebilir:

```text
Ambiguous command
```

Bu nedenle komutun hangi işlemi gerçekleştireceğini belirleyecek kadar karakter yazılmalıdır.

---

## 5. Ağ Arayüzü Yapılandırmasının CLI Üzerinden Görüntülenmesi

Bu bölümde daha önce LAB-1 kapsamında yapılandırmasını incelediğimiz `port3` ağ arayüzünün ayarları CLI üzerinden görüntülenecektir.

### 5.1. show Komutunun Kullanılması

Yeni bir satıra aşağıdaki komutu girin:

```bash
show system interface port3
```

Bu komut, `port3` arayüzünün yapılandırma bilgilerini görüntüler.

`show` komutu, varsayılan ayarlardan farklı olan yapılandırma değerlerini gösterir.

### 5.2. show full-configuration Komutunun Kullanılması

Şimdi aşağıdaki komutu çalıştırın:

```bash
show full-configuration system interface port3
```

Bu komut, `port3` arayüzüne ait tüm yapılandırma ayarlarını görüntüler.

Buna varsayılan değerler ve kullanıcı tarafından değiştirilmiş ayarlar da dahildir.

### 5.3. İki Komut Arasındaki Fark

**Düşün ve Değerlendir:**

`show` ve `show full-configuration` komutlarının çıktıları arasındaki fark nedir?

**Cevap:**

İki komut arasındaki temel fark, görüntülenen yapılandırma değerlerinin kapsamıdır.

| Komut | Açıklama |
|---|---|
| `show system interface port3` | Varsayılan değerlerden farklı olan yapılandırma ayarlarını gösterir. |
| `show full-configuration system interface port3` | Arayüzün tüm yapılandırma ayarlarını, varsayılan değerler dahil, gösterir. |

**Örnek:**

Bir arayüzün varsayılan MTU değeri 1500 ise ve bu değer değiştirilmemişse:

- `show` çıktısında MTU değeri görüntülenmeyebilir.
- `show full-configuration` çıktısında MTU değeri de görüntülenir.

Bu sayede yapılandırmanın yalnızca değiştirilen bölümlerini veya tüm ayrıntılarını inceleyebilirsiniz.

---

## 6. Laboratuvar Çalışmasının Özeti

Bu laboratuvar çalışmasında aşağıdaki işlemler gerçekleştirilmiştir:

- [x] FortiGate CLI arayüzüne giriş yapıldı.
- [x] `get system status` komutuyla sistem bilgileri görüntülendi.
- [x] CLI üzerindeki `More` ekranı incelendi.
- [x] `get ?` komutuyla yardım mekanizması kullanıldı.
- [x] Önceki komutlara erişim öğrenildi.
- [x] CLI klavye kısayolları denendi.
- [x] `execute ?` komutuyla kullanılabilir seçenekler görüntülendi.
- [x] `Tab` tuşuyla otomatik komut tamamlama özelliği denendi.
- [x] Komutların kısaltılarak kullanılabileceği öğrenildi.
- [x] `show system interface port3` komutuyla arayüz yapılandırması görüntülendi.
- [x] `show full-configuration system interface port3` komutuyla tüm yapılandırma ayarları incelendi.

## 7. Önemli CLI Komutları

Laboratuvarda kullanılan temel komutların özeti:

| Komut | Açıklama |
|---|---|
| `get system status` | FortiGate sistem durumunu görüntüler. |
| `get ?` | get komutunun seçeneklerini listeler. |
| `execute ?` | execute komutunun seçeneklerini listeler. |
| `show system interface port3` | port3 arayüzünün değiştirilmiş ayarlarını gösterir. |
| `show full-configuration system interface port3` | port3 arayüzünün tüm ayarlarını gösterir. |

---

**LAB-1 – Working With the CLI tamamlandı.**

Bu laboratuvar sayesinde FortiGate CLI arayüzünün temel kullanımını, komut yardım mekanizmasını, klavye kısayollarını ve yapılandırma görüntüleme komutlarını öğrendiniz.
