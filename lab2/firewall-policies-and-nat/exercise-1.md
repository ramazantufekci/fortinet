# LAB-2: Firewall Address Object ve Firewall Policy Oluşturma

Bu laboratuvarda **Firewall Address Object** (Güvenlik Duvarı Adres Nesnesi) oluşturacak ve bu nesneyi kullanarak bir **IPv4 Firewall Policy** yapılandıracaksınız.

Oluşturacağınız firewall policy içerisinde adres nesnelerinin yanı sıra zamanlama (Schedule), servisler (Services) ve log seçeneklerini de yapılandıracaksınız. Ardından bu policy üzerinden trafik geçirerek ve oluşan log kayıtlarını inceleyerek yapılandırmayı test edeceksiniz.

FortiGate'in temel işlevi bir güvenlik duvarı (Firewall) olmaktır. Bu nedenle, trafik üzerinde gerçekleştirdiği işlemlerin neredeyse tamamı firewall policy kurallarıyla ilişkilidir.

---

## 1. Firewall Address Object Oluşturma

FortiGate, fabrika varsayılanı (Factory Default) yapılandırmasında önceden tanımlanmış birçok bilinen adres nesnesiyle birlikte gelir. Ancak bu nesneler kuruluşunuzun ihtiyaçlarını karşılamıyorsa kendi adres nesnelerinizi oluşturabilirsiniz.

### Firewall Address Object oluşturmak için

1. **Local-FortiGate** GUI arayüzüne bağlanın.
2. Kullanıcı adı `admin` ve parola `password` bilgileriyle oturum açın.
3. **Policy & Objects > Addresses** menüsüne gidin.
4. **Create New > Address** seçeneğine tıklayın.
5. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `LOCAL_SUBNET` |
| Interface | `any` |
| Type | `Subnet` |
| IP/Netmask | `10.0.1.0/24` |

6. **OK** butonuna tıklayarak adres nesnesini kaydedin.

---

## 2. Firewall Policy Oluşturma

Öncelikle mevcut firewall policy kurallarından birini devre dışı bırakacaksınız. Ardından önceki adımda oluşturduğunuz adres nesnesini kullanarak daha özel bir firewall policy oluşturacaksınız.

Bu policy içerisinde belirli servisleri seçecek ve log ayarlarını yapılandıracaksınız.

### 2.1. Mevcut Firewall Policy'yi Devre Dışı Bırakma

1. **Local-FortiGate** GUI arayüzünde **Policy & Objects > Firewall Policy** menüsüne gidin.

   **Not:** FortiGate GUI, yeni policy listesi görünümüne geçmenizi isteyebilir. Klasik görünümle devam etmek için **Cancel** seçeneğine tıklayın. Yeni policy listesi görünümü, çok sayıda firewall policy bulunan ortamlarda performansı artırmak amacıyla tasarlanmıştır.

2. **port3 → port1** firewall policy bölümünü genişletin.
3. **Full_Access** isimli firewall policy üzerine sağ tıklayın.
4. **Set Status** alanından **Disable** seçeneğini seçin.

Böylece mevcut `Full_Access` policy devre dışı bırakılmış olacaktır.

### 2.2. Yeni Firewall Policy Oluşturma

1. **Policy & Objects > Firewall Policy** bölümünde kalın.
2. Yeni bir kural oluşturmak için **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `Internet_Access` |
| Incoming Interface | `port3` |
| Outgoing Interface | `port1` |
| Source | `LOCAL_SUBNET` |
| Destination | `all` |
| Schedule | `always` |
| Service | Aşağıdaki servisler |

**Service** alanına aşağıdaki servis nesnelerini ekleyin:

- `ALL_ICMP`
- `HTTP`
- `HTTPS`
- `DNS`
- `SSH`

**İpucu:** Servisleri hızlıca bulmak için arama kutusuna servis adını yazın. Ardından bulunan servis nesnesine tıklayarak policy içerisine ekleyin.

4. Log ayarlarını aşağıdaki şekilde yapılandırın:

| Alan | Değer |
|---|---|
| Log Allowed Traffic | `All Sessions` |
| Generate Logs when Session Starts | Etkin (`Enable`) |

5. Diğer tüm ayarları varsayılan değerlerinde bırakın.
6. **OK** butonuna tıklayarak firewall policy yapılandırmasını kaydedin.

**Önemli Bilgi – Stateful Firewall:**

FortiGate, stateful firewall (durum bilgisi tutan güvenlik duvarı) olarak çalışır. Bu nedenle bir oturumu başlatan trafiğin yönüyle eşleşen tek bir firewall policy oluşturmanız yeterlidir.

Oturumun dönüş trafiği, FortiGate tarafından oturum bilgileri kullanılarak takip edilir. Aynı iletişim için ters yönde ayrıca bir policy oluşturmanız gerekmez.

---

## 3. Firewall Policy'yi Test Etme ve Log Kayıtlarını İnceleme

Firewall policy yapılandırmasını tamamladınız. Şimdi bu policy üzerinden trafik geçirerek kuralın çalıştığını doğrulayacak ve oluşturulan log kayıtlarını inceleyeceksiniz.

### 3.1. Trafik Oluşturma

1. **Local-Client** sanal makinesine (VM) geçin.
2. Bir web tarayıcısı açın.
3. Birkaç farklı tarayıcı sekmesi açarak aşağıdaki harici web sitelerine bağlanın:

   - [www.google.com](https://www.google.com/)
   - [www.cnn.com](https://www.cnn.com/)
   - [www.bbc.com](https://www.bbc.com/)

Bu bağlantılar üzerinden internet trafiği oluşturarak `Internet_Access` firewall policy kuralını test etmiş olacaksınız.

### 3.2. Oluşturulan Log Kayıtlarını Görüntüleme

1. **Local-FortiGate** GUI arayüzünün açık olduğu tarayıcı sekmesine geri dönün.
2. **Policy & Objects > Firewall Policy** menüsüne gidin.
3. `Internet_Access` isimli policy üzerine sağ tıklayın.
4. **Show matching logs** seçeneğine tıklayın.

5. Açılan log kayıtlarında internet erişiminiz sırasında oluşan trafik kayıtlarını bulun.

Mevcut yapılandırmayla **Result** sütununda `Accept (Start)` değerini içeren birkaç log kaydı görmelisiniz.

Bu kayıtlar, oturumların başlatıldığını gösteren **Session Start Logs** kayıtlarıdır.

Oturumlar kapandığında ise gönderilen ve alınan veri miktarlarını gösteren ayrı bir log kaydı oluşturulur.

**Önemli Bilgi – Session Start Logları:**

Firewall policy içerisinde **Generate Logs when Session Starts** seçeneğini etkinleştirmek, oluşturulan log mesajlarının sayısını yaklaşık iki katına çıkarabilir.

Bu nedenle, oturum başlangıçlarının ayrıca kaydedilmesine gerçekten ihtiyaç duyulan durumlarda bu seçeneği etkinleştirmelisiniz. Aksi takdirde gereksiz log yoğunluğu oluşabilir.

**Önemli Bilgi – Policy UUID Filtresi:**

Firewall policy üzerinden **Show Matching Logs** seçeneğini kullandığınızda, Forward Traffic loglarına otomatik olarak **Policy UUID** filtresi eklenir.

### 3.3. Policy UUID Filtresini Kaldırma

1. **Forward Traffic** log ekranında bulunan `Policy UUID` filtresini bulun.
2. Filtrenin yanındaki **X** simgesine tıklayarak filtreyi kaldırın.

Böylece log kayıtları herhangi bir Policy UUID filtresi uygulanmadan görüntülenir.

**Not:** İlerleyen laboratuvar çalışmalarında bu filtre uygulanmamış Forward Traffic log kayıtlarını kullanacaksınız.

---

## 4. Laboratuvar Sonucu

Bu laboratuvar kapsamında aşağıdaki işlemleri gerçekleştirdiniz:

- `LOCAL_SUBNET` isimli Firewall Address Object oluşturdunuz.
- Mevcut `Full_Access` firewall policy kuralını devre dışı bıraktınız.
- `Internet_Access` isimli yeni bir IPv4 Firewall Policy oluşturdunuz.
- Policy içerisinde kaynak adres, hedef adres, arayüz ve Schedule ayarlarını yapılandırdınız.
- `ALL_ICMP`, `HTTP`, `HTTPS`, `DNS` ve `SSH` servislerini policy içerisine eklediniz.
- İzin verilen trafik için log ayarlarını etkinleştirdiniz.
- İstemci üzerinden internet trafiği oluşturarak firewall policy kuralını test ettiniz.
- Forward Traffic loglarını görüntülediniz ve Policy UUID filtresini kaldırdınız.

**Laboratuvar tamamlandı.**
