# LAB-9 – Uygulama Trafiğini Kontrol Etme (Controlling Application Traffic)

Bu laboratuvar çalışmasında FortiGate üzerinde profil tabanlı bir Application Control (Uygulama Kontrolü) profili oluşturacaksınız.

Yapılandırma sırasında **Flow-based Inspection** modu kullanılacaktır. Flow-based ve Proxy-based inceleme modlarında Application Control yapılandırma adımları aynıdır.

Ayrıca aşağıdaki işlemleri gerçekleştireceksiniz:

- Application Control filtrelerini yapılandırma.
- Belirli uygulamaların engellenmesini sağlama.
- Application Control profilini Firewall Policy üzerine uygulama.
- Uygulama kontrolünün çalışmasını test etme.
- Application Override yapılandırma.
- Uygulama kontrolü loglarını inceleme.
- FortiView üzerinden uygulama trafiğini izleme.

---

## 1. Filtre Geçersiz Kılmalarını Yapılandırma (Configure Filter Overrides)

Bu laboratuvar için kullanılan yapılandırma dosyasında Application Control kategorileri **Monitor** olarak ayarlanmıştır. Yalnızca **Unknown Applications** kategorisi bu ayarın dışındadır.

Monitor ayarı sayesinde uygulamalara izin verilir ancak uygulama trafiği için log kaydı oluşturulur.

Bu bölümde Filter Overrides yapılandırması gerçekleştirilecektir.

### Filter Overrides yapılandırmak için

1. Local-FortiGate GUI arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla oturum açın.
3. **Security Profiles > Application Control** menüsüne gidin.
4. Düzenlemek için **default** Application Control profiline çift tıklayın.

### Application Control imzaları hakkında

Application Control imza veritabanında, derinlemesine inceleme gerektiren 113 adet bulut tabanlı uygulama imzası bulunmaktadır.

> **Not:** Bulut tabanlı uygulama imzalarının sayısı zamanla değişebilir. Her kategoride bulut simgesinin yanında gösterilen sayı, ilgili kategoride bulunan bulut uygulaması imzalarının sayısını belirtir.

### Excessive-Bandwidth filtresi ekleme

1. **Application and Filter Overrides** bölümünde **Create New** seçeneğine tıklayın.
2. **Add New Override** sayfasındaki **Type** alanından **Filter** seçeneğini belirleyin.
3. Filtre eklemek için **+** simgesine tıklayın.
4. **BEHAVIOR** kategorisinin altında bulunan **Excessive-Bandwidth** seçeneğine tıklayın.
5. Seçimi onaylamak için **OK** seçeneğine tıklayın.

### Excessive-Bandwidth filtresinin açıklaması

**Excessive-Bandwidth** filtresi, yüksek bant genişliği kullandığı bilinen birçok uygulamayı engeller.

Bir uygulama farklı kategorilerde yer alabilir. Ancak yüksek bant genişliği tüketiyorsa bu davranış filtresinin kapsamına da girebilir.

6. Yapılandırmanın **Action** alanının **Block** olarak ayarlandığını kontrol edin.
7. Yapılandırmayı kaydetmek için **OK** seçeneğine tıklayın.

---

## 2. Application Control Profilini Firewall Policy Üzerine Uygulama

Application Control profili yapılandırıldıktan sonra ilgili güvenlik duvarı politikasına uygulanacaktır.

### Uzmanlık Görevi (Expert Challenge)

Local-FortiGate GUI üzerinde mevcut **Application_Control** isimli Firewall Policy'yi düzenleyerek aşağıdaki işlemleri gerçekleştirin:

- **default** Application Control profilini etkinleştirin.
- SSL/SSH Inspection profilinde **deep-inspection** seçeneğini etkinleştirin.

### Application Control profilini Firewall Policy üzerine uygulamak için

1. **Policy & Objects > Firewall Policy** menüsüne gidin.
2. Mevcut **Application_Control** politikasını düzenlemek için üzerine çift tıklayın.
3. **Security Profiles** bölümüne gidin.
4. **Application Control** seçeneğini etkinleştirin.
5. Application Control profil listesinden `default` seçeneğini belirleyin.
6. **SSL Inspection** alanından `deep-inspection` seçeneğini seçin.
7. Değişiklikleri kaydetmek için **OK** seçeneğine tıklayın.
8. Onay ekranında tekrar **OK** seçeneğine tıklayın.

> **Önemli:** FortiGate'in self-signed Full Inspection sertifikası tarayıcıya yüklenmemişse kullanıcılar sertifika uyarısı alabilir. Bu laboratuvar ortamında FortiGate'in self-signed SSL Inspection sertifikası tarayıcıya önceden yüklenmiştir.

---

## 3. Application Control Profilini Test Etme (Test the Application Control Profile)

Bu aşamada Application Override yapılandırmasında engellenen uygulamaya erişerek Application Control profilinin doğru çalışıp çalışmadığını test edeceksiniz.

### Application Control profilini test etmek için

1. Local-Client sanal makinesine geçin.
2. Tarayıcıda yeni bir sekme açın.
3. Aşağıdaki URL adresine gidin:

```text
http://abc.go.com
```

4. Web sitesine erişmeye çalışın.

Beklenen sonuç:

Web sitesine erişilememesi ve bağlantının zaman aşımına uğramasıdır (**Connection Timeout**).

Bu durum, FortiGate'in ilgili uygulama trafiğini engellediğini gösterir.

### Application Blocked mesajını etkinleştirme

FortiGate'in engelleme mesajını görüntülemesini sağlamak için:

1. Local-FortiGate GUI arayüzüne geri dönün.
2. **Security Profiles > Application Control** menüsüne gidin.
3. **default** Application Control profiline çift tıklayın.
4. Sayfanın altındaki **Options** bölümünü bulun.
5. **Replacement Messages for HTTP-based Applications** seçeneğini etkinleştirin.
6. **OK** seçeneğine tıklayarak değişiklikleri kaydedin.

### Engelleme mesajını tekrar test etme

1. Local-Client sanal makinesine dönün.
2. Tarayıcıda yeni bir sekme açın.
3. Aşağıdaki adrese tekrar gidin:

```text
http://abc.go.com
```

Beklenen sonuç:

FortiGate tarafından **Application Blocked** mesajı görüntülenmelidir.

> **Not:** Yapılandırma değişikliğinden sonra Application Blocked sayfasının görüntülenmesi 2 dakikaya kadar sürebilir.

Eğer 2 dakika geçmesine rağmen engelleme sayfası görüntülenmiyorsa:

- Tüm tarayıcı sekmelerini kapatın.
- Tarayıcıyı yeniden başlatın.
- Testi tekrar gerçekleştirin.

---

## 4. Application Overrides Yapılandırma

Bu aşamada Application Overrides yapılandırılacaktır.

**Application Overrides**, Filter Overrides ve Application Categories ayarlarından daha yüksek önceliğe sahiptir.

Yani belirli bir uygulama için tanımlanan özel kural, genel filtre veya kategori ayarlarının önüne geçebilir.

### Uzmanlık Görevi (Expert Challenge)

Local-FortiGate GUI üzerinde aşağıdaki işlemleri gerçekleştirin:

- **default** Application Control profilini düzenleyin.
- **ABC.Com** uygulama imzası için yeni bir Application Override oluşturun.
- Bu uygulama için Action değerini **Allow** olarak ayarlayın.

### Application Override oluşturmak için

1. Local-FortiGate GUI arayüzüne geri dönün.
2. **Security Profiles > Application Control** menüsüne gidin.
3. **default** Application Control profiline çift tıklayın.
4. **Application and Filter Overrides** bölümünde **Create New** seçeneğine tıklayın.
5. **Add New Override** sayfasındaki **Type** alanından **Application** seçeneğini belirleyin.
6. **Action** alanından **Allow** seçeneğini seçin.
7. Arama alanına aşağıdaki ifadeyi yazın:

```text
abc.com
```

8. `Enter` tuşuna basın.

FortiGate, arama sonucunda ilgili uygulama imzasını listeleyecektir.

9. Listeden **ABC.Com** uygulamasını seçin.
10. **OK** seçeneğine tıklayın.
11. Eklenen **ABC.Com** uygulama filtresini sürükleyerek **Excessive-Bandwidth** filtresinin üzerine taşıyın.

### Filtre sıralamasının önemi

ABC.Com uygulamasının Excessive-Bandwidth filtresinin üzerinde bulunması gerekir.

Bu sayede ABC.Com için tanımlanan özel Allow kuralı, genel Excessive-Bandwidth filtresinden önce değerlendirilir.

12. Yapılandırmayı kaydetmek için **OK** seçeneğine tıklayın.

> **Önemli:** Bu Application Control profili, giden tüm trafiği inceleyen bir Firewall Policy üzerine zaten uygulanmıştır. Bu nedenle yaptığınız değişikliklerin geçerli olması için Application Control profilini tekrar Firewall Policy üzerine uygulamanız gerekmez.

---

## 5. Application Overrides Yapılandırmasını Test Etme

Bu bölümde bir önceki aşamada izin verdiğiniz ABC.Com uygulamasına erişerek Application Override yapılandırmasını test edeceksiniz.

### Application Override test etmek için

1. Local-Client sanal makinesine geçin.
2. Tarayıcıda yeni bir sekme açın.
3. Aşağıdaki URL adresine gidin:

```text
http://abc.go.com
```

Beklenen sonuç:

**ABC.Com** uygulaması için Allow kuralı tanımlandığından web sitesi düzgün şekilde açılmalıdır.

Bu test, Application Override kuralının daha önce tanımlanan Excessive-Bandwidth engelleme filtresinden öncelikli olarak uygulandığını gösterir.

---

## 6. Application Control Loglarını ve Trafiğini İnceleme

Bu aşamada gerçekleştirdiğiniz test işlemlerine ait log kayıtlarını inceleyecek ve Application Control profiliyle eşleşen trafiği FortiView üzerinden görüntüleyeceksiniz.

### 6.1. Application Control Loglarını Görüntüleme

1. Local-FortiGate GUI arayüzüne geri dönün.
2. **Log & Report > Security Events** menüsüne gidin.
3. **Summary** bölümünün altındaki **Application Control** seçeneğine tıklayın.
4. **Application Name** log filtresini kullanın.
5. Arama alanına `ABC.Com` yazın.

Beklenen sonuç:

Action değeri **Block** olan log kayıtlarını göreceksiniz.

> **Not:** Bu kayıtlar, ABC.Com uygulamasının Application Override ile Allow olarak ayarlanmadan önce engellendiği test aşamasına aittir.

6. Daha fazla ayrıntı görüntülemek için ilgili log kaydına çift tıklayın.

### Application Control Log Detayları

Log detaylarında aşağıdaki bilgiler bulunur:

- Application Sensor Name: Kullanılan Application Control sensörünün adı.
- Application Name: Algılanan uygulamanın adı.
- Category: Uygulamanın kategorisi.
- Policy ID: Trafiği kontrol eden Firewall Policy kimliği.
- Action: FortiGate tarafından uygulanan işlem (Block, Allow vb.).

### 6.2. Forward Traffic Loglarını Görüntüleme

1. **Log & Report > Forward Traffic** menüsüne gidin.
2. Arama alanında `ABC.Com` uygulamasını arayın.
3. İlgili trafik kayıtlarını inceleyin.

Forward Traffic loglarında aşağıdaki ek bilgileri görüntüleyebilirsiniz:

- Translated IP Address: Çevrilmiş IP adresi.
- Bytes Sent: Gönderilen veri miktarı.
- Bytes Received: Alınan veri miktarı.
- Action: Trafiğe uygulanan işlem.
- Application: Algılanan uygulama.

---

## 7. FortiView Üzerinden Application Control Trafiğini İzleme

Bu bölümde Application Control profiliyle eşleşen uygulama trafiğini FortiView üzerinden inceleyeceksiniz.

### 7.1. FortiView Applications Ekranını Açma

1. Local-FortiGate GUI arayüzünde **Dashboard > FortiView Applications** menüsüne gidin.
2. Uygulama trafiği listesini görüntüleyin.
3. Listede **ABC.Com** uygulamasını bulun.

### 7.2. Uygulama Trafiği Detaylarını İnceleme

1. **ABC.Com** satırına tıklayarak uygulamayı seçin.
2. **Drill down** seçeneğine tıklayın.

Bu ekranda ABC.Com uygulamasına ait daha ayrıntılı trafik bilgileri görüntülenir.

### 7.3. İlgili Firewall Policy Bilgilerini Görüntüleme

1. Uygulama detay ekranında **Policies** seçeneğine tıklayın.
2. İlgili güvenlik duvarı politikasını inceleyin.
3. **View session logs** seçeneğine tıklayın.

Bu ekranda ABC.Com uygulamasıyla eşleşen oturumlara ait ayrıntıları görüntüleyebilirsiniz.

### Görüntülenebilen bilgiler

- Bytes: Gönderilen ve alınan toplam veri miktarı.
- Sessions: Oluşturulan oturum sayısı.
- Policy ID: Trafiği kontrol eden Firewall Policy kimliği.

Böylece Application Control profiliyle eşleşen ABC.Com uygulama trafiğini ayrıntılı şekilde incelemiş olursunuz.

---

## 8. Laboratuvar Çalışmasını Tamamlama

1. Local-FortiGate GUI oturumunu kapatın.

**LAB-9 – Controlling Application Traffic tamamlandı.**

---

## 9. Önemli Kavramlar – Hızlı Referans

| Kavram | Açıklama |
|---|---|
| Application Control | Ağ trafiğindeki uygulamaları tanımlar ve kontrol eder. |
| Application Signature | Bir uygulamanın ağ trafiğinden tanınmasını sağlayan imza. |
| Filter Override | Belirli özelliklere veya davranışlara göre uygulamalara kural uygular. |
| Application Override | Belirli bir uygulamaya özel Allow veya Block kuralı tanımlar. |
| Monitor | Uygulamaya izin verir ve log kaydı oluşturur. |
| Block | Uygulama trafiğini engeller. |
| Allow | Uygulama trafiğine izin verir. |
| Excessive-Bandwidth | Yüksek bant genişliği kullanan uygulamaları eşleştiren davranış filtresi. |
| Deep Inspection | SSL/TLS trafiğinin daha ayrıntılı incelenmesini sağlar. |
| FortiView Applications | Uygulama bazlı ağ trafiğini görselleştirir. |
| Drill down | Seçilen uygulamanın detaylarına iner. |

---

## 10. Laboratuvar Sonu Kontrol Listesi

- [ ] Local-FortiGate GUI oturumu açıldı.
- [ ] default Application Control profili düzenlendi.
- [ ] Excessive-Bandwidth filtresi eklendi.
- [ ] Filter Override Action değeri Block olarak ayarlandı.
- [ ] Application_Control Firewall Policy düzenlendi.
- [ ] Application Control etkinleştirildi.
- [ ] default profili seçildi.
- [ ] SSL Inspection deep-inspection olarak ayarlandı.
- [ ] abc.go.com adresine erişim test edildi.
- [ ] Application Blocked mesajı etkinleştirildi.
- [ ] ABC.Com için Application Override oluşturuldu.
- [ ] ABC.Com Action değeri Allow olarak ayarlandı.
- [ ] ABC.Com filtresi Excessive-Bandwidth filtresinin üzerine taşındı.
- [ ] ABC.Com erişiminin başarılı olduğu doğrulandı.
- [ ] Application Control logları incelendi.
- [ ] Forward Traffic logları incelendi.
- [ ] FortiView Applications ekranı incelendi.
- [ ] Drill down ile uygulama detayları görüntülendi.
- [ ] Policies ve View session logs ekranları incelendi.

**LAB-9 – Controlling Application Traffic başarıyla tamamlandı.**
