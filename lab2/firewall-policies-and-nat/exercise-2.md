# LAB-2: Firewall Policy Kurallarının Sıralamasını Değiştirme ve Policy İşlemleri

FortiGate, ilgili arayüz çifti (Interface Pair) için tanımlanmış firewall policy kurallarını yukarıdan aşağıya doğru kontrol eder. Trafik için eşleşen ilk policy bulunduğunda ilgili kural uygulanır.

Bu nedenle genellikle daha özel (Specific) kuralların listenin üst sıralarında bulunması gerekir. Aksi durumda daha genel kurallar trafikle önce eşleşir ve daha ayrıntılı kurallar hiçbir zaman uygulanmaz.

Bu laboratuvarda kaynak (Source), hedef (Destination) ve servis (Service) bilgileri belirli olan yeni bir firewall policy oluşturacaksınız. Bu policy için **DENY** aksiyonunu seçeceksiniz.

Ardından oluşturduğunuz policy kuralını mevcut firewall policy kurallarının üzerine taşıyarak sıralama değişikliğinin trafik üzerindeki etkisini inceleyeceksiniz.

---

## 1. Firewall Policy Oluşturma

Belirli bir kaynak, hedef ve servis ile eşleşen yeni bir firewall policy oluşturacak ve bu policy'nin Action değerini **DENY** olarak ayarlayacaksınız.

**Önemli Bilgi:**

`LINUX_ETH1` isimli firewall address object önceden yapılandırılmıştır.

| Özellik | Değer |
|---|---|
| Address Object | `LINUX_ETH1` |
| IP/Netmask | `10.200.1.254/32` |

Bu adres nesnesini yeni firewall policy içerisinde kullanacaksınız.

### Uzmanlık Görevi (Take the Expert Challenge!)

Local-FortiGate GUI üzerinde aşağıdaki ayarları kullanarak bir firewall policy oluşturun:

- Firewall policy adı: `Block_Ping`
- Incoming Interface: `port3`
- Outgoing Interface: `port1`
- Kaynak: `10.0.1.0/24` subnet'i
- Hedef: `10.200.1.254` IP adresi
- Kaynak adres nesnesi: `LOCAL_SUBNET`
- Hedef adres nesnesi: `LINUX_ETH1`
- Engellenecek servis: `PING`
- Action: `DENY`
- Log Violation Traffic: Etkin (`Enable`)

Bu işlemi kendiniz gerçekleştirmeyi deneyin. Yardıma ihtiyaç duyarsanız veya yapılandırmanızı doğrulamak isterseniz aşağıdaki adımları uygulayın.

### Firewall Policy oluşturmak için

1. **Local-FortiGate** GUI arayüzüne bağlanın.
2. Kullanıcı adı `admin` ve parola `password` bilgileriyle oturum açın.
3. **Policy & Objects > Firewall Policy** menüsüne gidin.
4. **Create New** butonuna tıklayın.
5. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `Block_Ping` |
| Incoming Interface | `port3` |
| Outgoing Interface | `port1` |
| Source | `LOCAL_SUBNET` |
| Destination | `LINUX_ETH1` |
| Service | `PING` |
| Action | `DENY` |
| Log Violation Traffic | Etkin (`Enable`) |

**İpucu:** `PING` servisini hızlıca bulmak için arama kutusuna servis adını yazın. Ardından servis nesnesine tıklayarak policy içerisine ekleyin.

6. **OK** butonuna tıklayarak yapılandırmayı kaydedin.

---

## 2. Firewall Policy Sıralamasını Değiştirerek Test Etme

Yapılandırma tamamlandı. Şimdi `Block_Ping` isimli firewall policy kuralını `Internet_Access` isimli firewall policy kuralının üzerine taşıyarak test gerçekleştireceksiniz.

Bu testte iki önemli noktayı doğrulayacaksınız:

- Policy sıralaması değiştirildiğinde trafik daha ayrıntılı (Granular) firewall policy ile eşleşmelidir.
- Policy sıralaması değişse bile policy ID değeri aynı kalmalıdır.

### 2.1. İlk Ping Testini Gerçekleştirme

1. **Local-Client** sanal makinesine (VM) geçin.
2. Bir terminal penceresi açın.
3. `Block_Ping` policy içerisinde hedef olarak tanımladığınız `LINUX_ETH1` adresine ping gönderin.

```bash
ping 10.200.1.254
```

<blockquote>
**Dur ve düşün!**

Az önce ping trafiğini engellemek için bir firewall policy oluşturmanıza rağmen hedef IP adresine neden hâlâ ping gönderebiliyorsunuz?
</blockquote>

**Açıklama:**

Ping işlemi hâlâ başarılıdır çünkü trafik oluşturduğunuz `DENY` policy ile değil, mevcut `ACCEPT` aksiyonuna sahip `Internet_Access` policy ile eşleşmektedir.

`Internet_Access` policy listede `Block_Ping` kuralının üzerinde bulunduğu için FortiGate önce bu genel policy'yi kontrol eder.

Trafik ilk eşleşen policy tarafından kabul edildiğinden `Block_Ping` policy hiç kontrol edilmez.

Bu durum, FortiGate'in firewall policy kurallarını yukarıdan aşağıya doğru kontrol ettiğini gösterir.

4. Terminal penceresini kapatmayın. Ping işlemi çalışmaya devam etsin.

### 2.2. Firewall Policy ID Sütununu Görüntüleme

1. **Local-FortiGate** GUI arayüzüne geçin.
2. **Policy & Objects > Firewall Policy** menüsüne gidin.

**Not:** Firewall Policy sayfasında `ID` sütunu zaten görünüyorsa bir sonraki bölüme geçebilirsiniz.

3. Fare imlecini **Name** sütununun üzerine getirin.

   Name başlığının yanında bir ayar simgesi görünecektir.

4. Ayar simgesine tıklayın.
5. Açılan menüde **Select Columns** bölümüne gidin.
6. **ID** sütununu seçin.
7. **Apply** butonuna tıklayın.

Böylece firewall policy tablosuna ID sütunu eklenecektir.

### 2.3. ID Sütununu İlk Sıraya Taşıma

1. Eklenen **ID** sütununu fareyle tutun.
2. **Name** sütununun sol tarafına sürükleyin.
3. Böylece ID sütunu tablonun ilk sütunu olacaktır.

4. Hem `Internet_Access` hem de `Block_Ping` firewall policy kurallarının mevcut **ID** değerlerini not edin.

**Önemli:** Bir sonraki adımda policy sıralamasını değiştirdiğinizde bu ID değerlerinin değişmediğini doğrulayacaksınız.

### 2.4. Block_Ping Policy Kuralını Üst Sıraya Taşıma

1. Firewall Policy tablosunda `Block_Ping` kuralını bulun.
2. Kuralı fareyle tutarak yukarı doğru sürükleyin.
3. `Internet_Access` policy kuralının üzerine bırakın.

Artık `Block_Ping` policy, `Internet_Access` policy'nin üzerinde yer almalıdır.

**Dikkat:** Policy sıralamasını değiştirdiğinizde `Block_Ping` kuralının ID değeri değişmez. Yalnızca policy'nin listedeki konumu değişir.

**Not:** Yaptığınız değişiklikler ekranda görünmüyorsa sayfayı yenileyin (Refresh). Alternatif olarak FortiGate GUI oturumunuzu kapatıp yeniden açabilirsiniz.

### 2.5. Ping Testini Tekrar Kontrol Etme

1. **Local-Client** sanal makinesine geri dönün.
2. Açık bıraktığınız ve sürekli ping gönderen terminal penceresini inceleyin.

Bu kez ping isteklerinin başarısız olduğunu görmelisiniz.

<blockquote>
**Dur ve düşün!**

Ping istekleri neden artık başarısız oluyor?
</blockquote>

**Açıklama:**

Bu durum, firewall policy sıralamasının trafik üzerindeki etkisini gösterir.

Daha ayrıntılı olan `Block_Ping` policy kuralını genel erişim kuralı olan `Internet_Access` üzerine taşıdınız.

FortiGate artık gelen ping trafiğini önce `Block_Ping` policy ile karşılaştırır. Trafik bu policy ile eşleştiği için `DENY` aksiyonu uygulanır ve ping trafiği engellenir.

3. Terminal penceresini kapatın.

---

## 3. Engellenen Trafiğin Log Kayıtlarını İnceleme

Ping trafiğinin engellendiğini log kayıtları üzerinden de doğrulayabilirsiniz.

1. **Local-FortiGate** GUI arayüzüne geçin.
2. **Log & Report > Forward Traffic** menüsüne gidin.
3. Forward Traffic log kayıtlarını inceleyin.

`Block_Ping` policy tarafından engellenen ping trafiğine ait çok sayıda **Policy Violation** log kaydı görmelisiniz.

**Not:** Önceki laboratuvarda uyguladığınız log filtresini temizleyin. Böylece trafik kayıtlarını filtre uygulanmadan görüntüleyebilirsiniz.

---

## 4. Laboratuvar Sonucu

Bu laboratuvar kapsamında aşağıdaki işlemleri gerçekleştirdiniz:

- `Block_Ping` isimli yeni bir firewall policy oluşturdunuz.
- Kaynak olarak `LOCAL_SUBNET`, hedef olarak `LINUX_ETH1` adres nesnelerini kullandınız.
- Yalnızca `PING` servisini kapsayan bir policy tanımladınız.
- Action değerini `DENY` olarak ayarladınız.
- `Log Violation Traffic` seçeneğini etkinleştirdiniz.
- Policy sıralaması değiştirilmeden önce ping trafiğinin neden engellenmediğini gözlemlediniz.
- `Block_Ping` policy kuralını `Internet_Access` kuralının üzerine taşıdınız.
- Policy sıralaması değiştiğinde ID değerinin aynı kaldığını doğruladınız.
- Policy sıralaması değiştikten sonra ping trafiğinin engellendiğini gözlemlediniz.
- Forward Traffic logları üzerinden engellenen trafiği incelediniz.

### Öğrenilmesi Gereken Temel Kavram

**FortiGate firewall policy kurallarını yukarıdan aşağıya doğru kontrol eder ve trafikle eşleşen ilk policy kuralını uygular.**

Bu nedenle daha özel (Specific) kurallar, daha genel kurallardan önce yer almalıdır.

**LAB-2 tamamlandı.**
