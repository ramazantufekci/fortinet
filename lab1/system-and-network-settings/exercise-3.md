# LAB-1: FortiGate Yapılandırma Yedeklerinin Oluşturulması

## 1. Amaç

Bu laboratuvar çalışmasında FortiGate yapılandırmasının nasıl yedekleneceği ve daha önce alınmış yedeklerden nasıl geri yükleneceği öğrenilecektir.

Bu çalışma kapsamında:

- Mevcut FortiGate yapılandırması yedekten geri yüklenecektir.
- Şifresiz (Cleartext) yapılandırma yedekleri incelenecektir.
- Şifrelenmiş (Encrypted) yapılandırma yedeği oluşturulacaktır.
- Şifrelenmiş yapılandırma yedeğinden geri yükleme yapılacaktır.
- İki farklı yapılandırma dosyasının başlık bilgileri karşılaştırılacaktır.

Yapılandırma yedekleri, FortiGate cihazını daha önceki bir yapılandırma durumuna döndürmek için kullanılır.

---

## 2. Yedekten Yapılandırmanın Geri Yüklenmesi

Bu bölümde daha önceden hazırlanmış bir yapılandırma dosyası kullanılarak FortiGate cihazının ayarları geri yüklenecektir.

### 2.1. Local-Client Sanal Makinesine Giriş

1. Local-Client sanal makinesine (VM) bağlanın.
2. Aşağıdaki kullanıcı bilgileriyle oturum açın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `Administrator` |
| Şifre | `password` |

**Not:** İlk kez oturum açıyorsanız giriş ekranını görüntülemek için ekranın alt kısmından yukarı doğru tıklayıp sürüklemeniz gerekebilir.

### 2.2. Local-FortiGate GUI Arayüzüne Bağlanma

1. Local-Client VM üzerinde bir web tarayıcısı açın.
2. Adres çubuğuna aşağıdaki IP adresini girin:

```text
10.0.1.254
```

3. Local-FortiGate GUI arayüzüne aşağıdaki bilgilerle giriş yapın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `admin` |
| Şifre | `password` |

**Önemli Not:**

Fortinet laboratuvar çalışmalarında tutarlı sonuçlar elde etmek için Local-Client ve Remote-Client sanal makinelerinde internet erişimi ve FortiGate GUI bağlantıları için Mozilla Firefox kullanılması önerilir.

Ayrıca Firefox yer imleri (Bookmarks) çubuğundan Local-FortiGate GUI arayüzüne doğrudan erişebilirsiniz.

### 2.3. Yapılandırma Yedeğinin Geri Yüklenmesi

1. FortiGate GUI arayüzünün sağ üst köşesindeki **admin** menüsüne tıklayın.
2. **Configuration > Restore** yolunu izleyin.

3. Açılan **Restore System Configuration** ekranında **Upload** butonuna tıklayın.

4. Bilgisayarınızda bulunan yedek yapılandırma dosyasını seçin.

5. Aşağıdaki klasör yolunu takip edin:

```text
Desktop
└── Resources
    └── FortiGate-Administrator
        └── Introduction
            └── local-initial.conf
```

6. `local-initial.conf` dosyasını seçin.
7. **Select** butonuna tıklayın.
8. Ardından **OK** butonuna tıklayın.
9. Yapılandırmanın geri yüklenmesini onaylamak ve cihazı yeniden başlatmak için tekrar **OK** butonuna tıklayın.

**Önemli:**

Yapılandırma dosyası tarayıcı üzerinden FortiGate'e yüklendikten sonra cihaz otomatik olarak yeniden başlatılır.

Yeniden başlatma işlemi yaklaşık **30-45 saniye** sürer.

### 2.4. Geri Yükleme İşleminin Kontrol Edilmesi

1. FortiGate yeniden başladıktan sonra GUI giriş ekranının tekrar açılmasını bekleyin.
2. Aşağıdaki bilgilerle tekrar giriş yapın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `admin` |
| Şifre | `password` |

3. **Network > Interfaces** bölümüne gidin.
4. Ağ arayüzü ayarlarının geri yüklenip yüklenmediğini kontrol edin.

5. Ardından **Network > Static Routes** bölümüne gidin.
6. Varsayılan rotanın (Default Route) geri yüklenmiş olduğunu doğrulayın.

Bu işlem sonucunda FortiGate cihazının ağ arayüzü ve statik yönlendirme ayarları yedek dosyasındaki yapılandırmaya göre geri yüklenmiş olmalıdır.

---

## 3. Yapılandırma Dosyasının Yedeklenmesi ve Şifrelenmesi

FortiGate üzerinde herhangi bir yapılandırma değişikliği yapmadan önce mutlaka yedek alınmalıdır.

Yapılacak değişiklik küçük veya önemsiz görünse bile yedekleme işlemi ihmal edilmemelidir.

FortiGate üzerinde klasik bir geri alma (Undo) mekanizması bulunmadığından, hatalı yapılandırma değişiklikleri sorunlara neden olabilir.

### 3.1. Şifrelenmiş Yapılandırma Yedeği Oluşturma

1. Local-Client VM üzerinde Firefox tarayıcısını açın.
2. Local-FortiGate GUI arayüzüne aşağıdaki adres üzerinden bağlanın:

```text
10.0.1.254
```

3. Kullanıcı bilgileriyle giriş yapın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `admin` |
| Şifre | `password` |

4. Sağ üst köşedeki **admin** menüsüne tıklayın.
5. **Configuration > Backup** yolunu izleyin.

6. Açılan **Backup System Configuration** ekranında **Encryption** seçeneğini etkinleştirin.

7. **Password** alanına aşağıdaki şifreyi girin:

```text
fortinet
```

8. **Confirm password** alanına aynı şifreyi tekrar girin:

```text
fortinet
```

9. **OK** butonuna tıklayın.

### 3.2. Yedek Dosyasının Kaydedilmesi

Yedekleme işlemi tamamlandığında Firefox, şifrelenmiş yapılandırma dosyasını varsayılan olarak **Downloads** klasörüne indirir.

İndirilen dosyaya tarayıcının sağ üst köşesinde bulunan indirme simgesi üzerinden ulaşabilirsiniz.

**Önemli Güvenlik Notu:**

Şifrelenmiş yedek oluşturduğunuzda kullandığınız parolayı mutlaka kaydedin ve güvenli bir yerde saklayın.

Parolayı unutmanız durumunda yedek dosyasının içeriğini çözüp FortiGate'e geri yükleyemezsiniz.

### 3.3. Şifreli ve Şifresiz Yedeklerin Karşılaştırılması

FortiGate yapılandırma yedekleri iki farklı şekilde saklanabilir:

| Özellik | Cleartext (Şifresiz) | Encrypted (Şifrelenmiş) |
|---|---|---|
| Okunabilirlik | Metin editörüyle okunabilir. | Şifre çözülmeden okunamaz. |
| Güvenlik | Dosya güvenli bir yerde saklanmalıdır. | Şifreleme sayesinde daha iyi korunur. |
| Sorun giderme | İncelenmesi daha kolaydır. | İncelenmesi daha zordur. |
| Parola gereksinimi | Şifre çözme parolası gerekmez. | Yedekten geri yüklemek için parola gerekir. |
| Fortinet Support incelemesi | İçerik incelenebilir. | Şifreli dosya sorun gidermeyi zorlaştırır. |

**Önemli Tavsiye:**

Fortinet, yedeklerin güvenli bir ortamda saklanması koşuluyla şifresiz olarak tutulmasının bazı sorun giderme işlemlerini kolaylaştırabileceğini belirtir.

Özellikle özel anahtarlar (Private Keys) gibi hassas bilgiler içeren yapılandırma dosyaları yetkisiz erişime karşı korunmalıdır.

---

## 4. Şifrelenmiş Yapılandırma Yedeğinin Geri Yüklenmesi

Bu bölümde bir önceki aşamada oluşturulan şifrelenmiş yapılandırma yedeği FortiGate cihazına geri yüklenecektir.

Şifrelenmiş yedeklerin geri yüklenebilmesi için oluşturulurken kullanılan parolanın bilinmesi gerekir.

**Dikkat:** Şifreyi unutmanız durumunda ilgili yedeği geri yükleyemezsiniz.

### 4.1. Şifrelenmiş Yedeğin Geri Yüklenmesi

1. Local-FortiGate GUI arayüzüne giriş yapın.
2. Sağ üst köşedeki **admin** menüsüne tıklayın.
3. **Configuration > Restore** yolunu izleyin.

4. Açılan **Restore System Configuration** ekranında **Upload** butonuna tıklayın.

5. Bilgisayarınızın **Downloads** klasörüne gidin.
6. Bir önceki bölümde oluşturduğunuz şifrelenmiş yapılandırma dosyasını seçin.

7. **Password** alanına yedek oluştururken kullandığınız parolayı girin:

```text
fortinet
```

8. **OK** butonuna tıklayın.
9. Yapılandırmanın geri yüklenmesini onaylamak için tekrar **OK** butonuna tıklayın.

FortiGate, yapılandırmayı geri yükledikten sonra otomatik olarak yeniden başlatılır.

### 4.2. Expert Challenge (Uzmanlık Görevi)

**Görev:** Bir önceki bölümde oluşturduğunuz şifrelenmiş yapılandırma yedeğini kullanarak FortiGate cihazını geri yükleyin.

Yardım gerekirse veya yaptığınız işlemi doğrulamak isterseniz yukarıdaki adım adım yönergeleri kullanabilirsiniz.

---

## 5. İki Yapılandırma Dosyasının Başlıklarının Karşılaştırılması

Bu bölümde iki farklı yapılandırma dosyası Notepad++ kullanılarak açılacak ve başlık bilgileri karşılaştırılacaktır.

Bu işlem özellikle aşağıdaki durumlarda önemlidir:

- FortiGate sorunlarının giderilmesi.
- Önceki bir FortiOS sürümüne geri dönülmesi.
- Farklı firmware sürümleri arasında yapılandırma karşılaştırması yapılması.
- Yapılandırma dosyasının hangi FortiGate modeli ve firmware sürümü için oluşturulduğunun belirlenmesi.

### 5.1. Notepad++ Uygulamasını Açma

1. Local-Client VM üzerinde Notepad++ uygulamasını açın.
2. **File > Open** menüsüne tıklayın.
3. **Downloads** klasörüne gidin.
4. Daha önce oluşturduğunuz şifrelenmiş yapılandırma dosyasını seçin.

### 5.2. Başlangıç Yapılandırma Dosyasını Açma

1. Notepad++ üzerinde tekrar **File > Open** menüsüne tıklayın.
2. Aşağıdaki klasör yoluna gidin:

```text
Desktop\Resources\FortiGate-Security\Introduction\local-initial.conf
```

3. `local-initial.conf` dosyasını açın.

Bu dosya Notepad++ üzerinde ikinci bir sekmede görüntülenecektir.

### 5.3. Yapılandırma Dosyalarının Başlıklarını Karşılaştırma

Notepad++ üzerinde açık olan iki yapılandırma dosyasının başlık bilgilerini karşılaştırın.

İncelenecek dosyalar:

1. Daha önce oluşturduğunuz şifrelenmiş yapılandırma yedeği.
2. Başlangıç yapılandırma dosyası olan `local-initial.conf`.

#### Şifrelenmiş Yapılandırma Dosyası

Şifrelenmiş yapılandırma dosyasının içeriği doğrudan okunabilir değildir.

#### Şifresiz Yapılandırma Dosyası

Şifresiz yapılandırma dosyasının içeriği metin formatında okunabilir ve incelenebilir.

**Önemli Bilgi:**

Hem şifresiz (Cleartext) hem de şifrelenmiş (Encrypted) yapılandırma dosyalarında ilk satır başlık (Header) görevi görür.

Bu başlıkta yapılandırma dosyasının ait olduğu:

- Firmware sürümü
- FortiGate modeli

bilgileri bulunur.

Bu bilgiler, yapılandırma dosyasının hangi FortiGate cihazına ve yazılım sürümüne ait olduğunu anlamak açısından önemlidir.

### 5.4. İşlemi Tamamlama

1. Notepad++ üzerindeki iki dosyanın sekmelerini kapatın.
2. Notepad++ uygulamasını kapatın.

---

## 6. Laboratuvar Çalışmasının Özeti

Bu laboratuvar çalışmasında aşağıdaki işlemler gerçekleştirilmiştir:

- [x] Local-Client sanal makinesine giriş yapıldı.
- [x] Local-FortiGate GUI arayüzüne bağlanıldı.
- [x] `local-initial.conf` yapılandırma dosyası kullanılarak geri yükleme yapıldı.
- [x] Ağ arayüzü ayarları kontrol edildi.
- [x] Statik varsayılan rota kontrol edildi.
- [x] FortiGate yapılandırmasının yedeği oluşturuldu.
- [x] Şifreleme (Encryption) özelliği etkinleştirildi.
- [x] Yedekleme parolası `fortinet` olarak belirlendi.
- [x] Şifrelenmiş yapılandırma dosyası indirildi.
- [x] Şifrelenmiş yedek kullanılarak geri yükleme yapıldı.
- [x] Notepad++ ile yapılandırma dosyaları açıldı.
- [x] Şifreli ve şifresiz yapılandırma dosyalarının başlıkları karşılaştırıldı.
- [x] Firmware ve FortiGate model bilgilerinin yapılandırma dosyasının başlığında bulunduğu öğrenildi.

---

## 7. Önemli GUI Menüleri

| Menü | İşlev |
|---|---|
| `admin > Configuration > Backup` | Yapılandırma yedeği oluşturur. |
| `admin > Configuration > Restore` | Daha önce alınmış yapılandırma yedeğini geri yükler. |
| `Network > Interfaces` | Ağ arayüzlerini görüntüler ve yapılandırır. |
| `Network > Static Routes` | Statik yönlendirme ayarlarını görüntüler ve yapılandırır. |

## 8. Önemli Kavramlar

| Kavram | Açıklama |
|---|---|
| Configuration Backup | FortiGate yapılandırmasının yedeklenmesi. |
| Configuration Restore | Yedek yapılandırmanın geri yüklenmesi. |
| Cleartext | Şifrelenmemiş, okunabilir yapılandırma dosyası. |
| Encrypted | Şifrelenmiş, doğrudan okunamayan yapılandırma dosyası. |
| Encryption | Şifreleme işlemi. |
| Decryption | Şifre çözme işlemi. |
| Firmware | FortiGate cihazında çalışan sistem yazılımı. |
| Header | Yapılandırma dosyasının başlık bilgileri. |
| Private Key | Özel anahtar. |
| FortiOS | FortiGate cihazlarının işletim sistemi. |

---

## 9. Önemli Güvenlik ve Yedekleme Tavsiyeleri

1. FortiGate üzerinde değişiklik yapmadan önce mutlaka yapılandırma yedeği alın.
2. Yedek dosyalarını güvenli bir ortamda saklayın.
3. Şifrelenmiş yedeklerin parolalarını güvenli bir parola yöneticisinde muhafaza edin.
4. Yedek dosyalarını yetkisiz kişilerin erişimine açık bırakmayın.
5. Geri yükleme yapmadan önce doğru cihaz ve firmware sürümüne ait dosyayı seçtiğinizden emin olun.
6. Mümkünse yedeklerin geri yüklenebilir olduğunu düzenli olarak test edin.

---

**LAB-1 – Generating Configuration Backups tamamlandı.**

Bu laboratuvar sayesinde FortiGate yapılandırmasının yedeklenmesi, şifrelenmesi, geri yüklenmesi ve yapılandırma dosyalarının incelenmesi öğrenilmiştir.
