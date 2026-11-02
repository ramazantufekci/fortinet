# LAB-2: VIP Kullanarak DNAT Ayarlarının Yapılandırılması

VIP (Virtual IP), genellikle harici (Public) IP adreslerinin dahili (Private) IP adreslerine yönlendirilmesi ve çevrilmesi amacıyla kullanılır.

Bu laboratuvarda Local-Client sanal makinesi için bir VIP yapılandıracaksınız. Ardından oluşturduğunuz VIP nesnesini hedef adres olarak kullanan, dışarıdan içeriye doğru çalışan (Ingress) bir firewall policy oluşturacaksınız.

Bu yapılandırma sayesinde internet üzerinden Local-Client sanal makinesine HTTP ve HTTPS bağlantıları yapılabilecektir.

Ayrıca CLI komutlarını kullanarak DNAT ve SNAT davranışlarını inceleyeceksiniz.

---

## 1. VIP Oluşturma

FortiGate üzerinde DNAT (Destination Network Address Translation) işlemi gerçekleştirmek için VIP nesnesini firewall policy içerisindeki **Destination** alanında kullanırsınız.

Bu laboratuvarda VIP üzerinden aşağıdaki IP adresi eşleştirmesini yapacaksınız:

| Özellik | Değer |
|---|---|
| Local-Client IP adresi | `10.0.1.10` |
| VIP External IP adresi | `10.200.1.200` |
| Arayüz | `port1` |

`10.200.1.200` adresi, `port1` arayüzünün bağlı olduğu subnet içerisinde yer almaktadır.

### VIP oluşturmak için

1. **Local-FortiGate** GUI arayüzüne bağlanın.
2. Kullanıcı adı `admin` ve parola `password` bilgileriyle oturum açın.
3. **Policy & Objects > Virtual IPs** menüsüne gidin.
4. **Create New** butonuna tıklayın.
5. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `VIP-INTERNAL-HOST` |
| Interface | `port1` |
| External IP address/range | `10.200.1.200` |
| Map to IPv4 address/range | `10.0.1.10` |

**Interface açıklaması:**

`port1`, internete bağlı olan arayüzdür. Bu arayüzün IP adresi `10.200.1.1/24` şeklindedir.

**External IP address/range açıklaması:**

`10.200.1.200` adresi, `port1` subnet'i içerisinde bulunan ve dışarıdan erişilecek VIP adresidir.

**Map to IPv4 address/range açıklaması:**

Bu alan, gelen bağlantıların yönlendirileceği dahili IP adresini belirtir. Bu laboratuvarda Local-Client makinesinin IP adresi olan `10.0.1.10` kullanılacaktır.

6. **OK** butonuna tıklayarak VIP nesnesini kaydedin.

---

## 2. VIP Kullanarak Firewall Policy Oluşturma

Şimdi oluşturduğunuz VIP nesnesini hedef adres olarak kullanan yeni bir firewall policy oluşturacaksınız.

Bu policy, dış ağdan (port1) gelen HTTP ve HTTPS bağlantılarının iç ağa (port3) yönlendirilmesini sağlayacaktır.

### Firewall Policy oluşturmak için

1. **Local-FortiGate** GUI arayüzünde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `Web-Server-Access` |
| Incoming Interface | `port1` |
| Outgoing Interface | `port3` |
| Source | `all` |
| Destination | `VIP-INTERNAL-HOST` |
| Service | `HTTP`, `HTTPS` |

**İpucu:** `VIP-INTERNAL-HOST` nesnesi, Destination alanında **VIRTUAL IP/SERVER** bölümünün altında listelenir.

**HTTP ve HTTPS servislerini eklemek için:**

Sağ taraftaki servis arama kutusuna servis adını yazın. Bulduğunuz servis nesnesine tıklayarak policy içerisine ekleyin.

4. **Firewall/Network Options** bölümüne gidin.
5. **NAT** seçeneğini devre dışı bırakın (`Disable`).

**Önemli:** Bu policy üzerinde NAT seçeneği kapalı olacaktır. VIP nesnesi, gelen bağlantılar için DNAT işlemini zaten gerçekleştirir. Bu aşamada ayrıca SNAT uygulanması istenmemektedir.

6. **Logging Options** bölümüne gidin.
7. **All Sessions** seçeneğini işaretleyin.
8. **OK** butonuna tıklayarak firewall policy yapılandırmasını kaydedin.

---

## 3. VIP Firewall Policy Yapılandırmasını Test Etme

VIP hedef adres olarak kullanılarak firewall policy oluşturuldu. Şimdi bu yapılandırmayı **Remote-Client** sanal makinesinden test edeceksiniz.

Remote-Client, Remote-FortiGate'in dahili ağında bulunmaktadır. İki FortiGate cihazı arasında bir Linux makinesi yönlendirici (Router) olarak görev yapar ve Remote-FortiGate üzerinden gelen trafiği Local-FortiGate'e iletir.

Bu ağ yapısının ayrıntıları için eğitim dokümanındaki Network Topology diyagramını inceleyebilirsiniz.

Bu testte ayrıca Local-Client makinesinden dışarıya çıkan trafik sırasında kaynak adresinin nasıl çevrildiğini de inceleyeceksiniz.

### 3.1. DNAT İşlemini Test Etme

1. **Remote-Client** sanal makinesine geçin.
2. Bir web tarayıcısı açın.
3. Adres çubuğuna aşağıdaki URL'yi yazın:

   [http://10.200.1.200](http://10.200.1.200)

4. VIP yapılandırması doğru çalışıyorsa basit bir web sayfası açılacaktır.

Bu işlem sırasında Remote-Client, `10.200.1.200` VIP adresine HTTP bağlantısı kurar. FortiGate gelen bağlantının hedef adresini `10.0.1.10` olarak değiştirerek Local-Client makinesine iletir.

### 3.2. CLI Üzerinden DNAT Kayıtlarını İnceleme

Şimdi FortiGate CLI üzerinden oturum tablosunu inceleyerek DNAT işlemini doğrulayacaksınız.

1. **Local-FortiGate** CLI oturumunu açın.
2. Kullanıcı adı `admin` ve parola `password` bilgileriyle oturum açın.
3. Aşağıdaki komutu çalıştırın:

```bash
get system session list
```

Bu komut, FortiGate üzerindeki aktif oturumları listeler.

Örnek çıktı:

```text
Local-FortiGate# get system session list

PROTO EXPIRE SOURCE          SOURCE-NAT DESTINATION       DESTINATION-NAT

tcp   3594   10.200.3.1:49478 -          10.200.1.200:80  10.0.1.10:80
```

Çıktıda aşağıdaki adres dönüşümünü görebilirsiniz:

| Alan | IP adresi |
|---|---|
| SOURCE | `10.200.3.1:49478` |
| DESTINATION | `10.200.1.200:80` |
| DESTINATION-NAT | `10.0.1.10:80` |

**Açıklama:**

- `10.200.1.200:80`: İstemcinin bağlantı kurduğu VIP adresi ve HTTP portudur.
- `10.0.1.10:80`: DNAT sonrasında bağlantının yönlendirildiği dahili IP adresi ve HTTP portudur.

Böylece VIP üzerinde tanımladığınız IP adresi eşleştirmesinin çalıştığını doğrulamış olursunuz.

**Not:** `get system session list` komutunu çalıştırdığınız sırada HTTP oturumu kapanmış veya silinmiş olabilir. Böyle bir durumda 3.1 bölümündeki 1-3 numaralı adımları tekrarlayarak yeni bir HTTP bağlantısı oluşturun. Ardından CLI komutunu tekrar çalıştırın.

---

## 4. SNAT İşlemini Test Etme

VIP, Static NAT olarak yapılandırıldığında FortiGate'in SNAT (Source Network Address Translation) davranışını da etkiler.

Normalde iç ağdan dış ağa çıkan trafik için NAT etkinleştirildiğinde, FortiGate çoğunlukla çıkış arayüzünün IP adresini SNAT adresi olarak kullanır.

Ancak Static NAT VIP kullanıldığında, belirli koşullar altında VIP'nin External IP adresi SNAT adresi olarak tercih edilir.

Bu laboratuvarda bu davranışı inceleyeceksiniz.

### 4.1. Mevcut Oturumları Temizleme

1. Açık olan **Local-FortiGate CLI** oturumuna geri dönün.
2. Mevcut oturumları temizlemek için aşağıdaki komutu çalıştırın:

```bash
diagnose sys session clear
```

Bu komut FortiGate üzerindeki tüm aktif oturumları temizler.

**Önemli Bilgi:**

`diagnose sys session clear` komutu, oluşturduğunuz SSH oturumu da dahil olmak üzere tüm oturumları temizler.

Bu nedenle komutu çalıştırdıktan sonra CLI bağlantınızın kesilmesi normaldir. Local-Client VM üzerinden Local-FortiGate'e olan mevcut bağlantı oturumu da temizlenmiş olur.

### 4.2. Local-Client Üzerinden İnternet Trafiği Oluşturma

1. Local-FortiGate CLI penceresini kapatın.
2. **Local-Client** sanal makinesine geçin.
3. Bir web tarayıcısı açın.
4. Birkaç farklı tarayıcı sekmesi açarak aşağıdaki web sitelerine bağlanın:

   - [www.fortinet.com](https://www.fortinet.com/)
   - [www.yahoo.com](https://www.yahoo.com/)
   - [www.bbc.com](https://www.bbc.com/)

Bu bağlantılar sayesinde Local-Client üzerinden dış ağa doğru yeni oturumlar oluşturulacaktır.

### 4.3. SNAT Adresini CLI Üzerinden İnceleme

1. **Local-FortiGate** CLI arayüzünü yeniden açın.
2. Kullanıcı adı `admin` ve parola `password` bilgileriyle oturum açın.
3. Aktif oturumları görüntülemek için aşağıdaki komutu çalıştırın:

```bash
get system session list
```

Örnek oturum çıktısını inceleyin.

Bu aşamada Local-Client üzerinden dışarıya yapılan bağlantıların kaynak adresi dönüşümünü görebilirsiniz.

**Önemli Bilgi:**

Local-Client VM üzerinden dışarıya yapılan bağlantılarda SNAT adresi olarak `10.200.1.1` (Firewall çıkış arayüzünün IP adresi) yerine VIP adresi olan `10.200.1.200` kullanılacaktır.

| Özellik | IP adresi |
|---|---|
| Local-Client dahili IP adresi | `10.0.1.10` |
| Normal çıkış arayüzü IP adresi | `10.200.1.1` |
| VIP External IP adresi | `10.200.1.200` |
| SNAT için kullanılan adres | `10.200.1.200` |

**Açıklama:**

Bu davranış, Static NAT VIP kullanıldığında SNAT işleminin nasıl gerçekleştiğini gösterir.

Eşleşen çıkış yönlü firewall policy üzerinde NAT etkinse ve bağlantının kaynak adresi VIP'nin dahili adresiyle eşleşiyorsa FortiGate, SNAT için çıkış arayüzünün IP adresi yerine Static NAT VIP'nin External IP adresini kullanır.

Yani VIP üzerinde tanımlanan statik NAT eşleştirmesi, uygun koşullarda hem DNAT hem de SNAT davranışını etkiler.

4. Local-FortiGate CLI penceresini kapatın.
5. Local-FortiGate GUI sekmesi dışındaki tüm tarayıcı sekmelerini kapatın.

---

## 5. Laboratuvar Sonucu

Bu laboratuvar kapsamında aşağıdaki işlemleri gerçekleştirdiniz:

- `VIP-INTERNAL-HOST` isimli Virtual IP (VIP) nesnesi oluşturdunuz.
- `10.200.1.200` harici IP adresini `10.0.1.10` dahili IP adresine eşlediniz.
- VIP nesnesini Destination olarak kullanan `Web-Server-Access` firewall policy oluşturdunuz.
- HTTP ve HTTPS servislerine izin verdiniz.
- Gelen bağlantılar için policy üzerindeki NAT seçeneğini devre dışı bıraktınız.
- Remote-Client üzerinden VIP adresine erişerek DNAT işlemini test ettiniz.
- `get system session list` komutuyla DNAT adres dönüşümünü incelediniz.
- Mevcut oturumları `diagnose sys session clear` komutuyla temizlediniz.
- Local-Client üzerinden internet bağlantıları oluşturdunuz.
- Static NAT VIP kullanıldığında SNAT adresinin nasıl seçildiğini gözlemlediniz.

### Öğrenilmesi Gereken Temel Kavramlar

**DNAT:** Gelen trafiğin hedef IP adresini değiştirerek dahili bir IP adresine yönlendirir.

**SNAT:** Giden trafiğin kaynak IP adresini değiştirir.

**VIP (Virtual IP):** FortiGate üzerinde harici IP adresleri ile dahili IP adresleri arasında eşleştirme yapılmasını sağlar.

**Static NAT VIP:** Uygun koşullarda hem DNAT hem de SNAT işlemlerinde kullanılabilir.

**LAB-2 tamamlandı.**
