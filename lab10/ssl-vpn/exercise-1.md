# LAB-10 – SSL VPN Tünel Modunu Yapılandırma (Configuring SSL VPN Tunnel Mode)

Bu laboratuvar çalışmasında FortiGate üzerinde SSL VPN ayarlarını yapılandırarak uzak kullanıcıların yerel ağdaki (`10.0.1.0/24`) kaynaklara güvenli şekilde erişmesini sağlayacaksınız.

Bağlantı, Remote-Client sanal makinesi üzerinden **Tunnel Mode (Tünel Modu)** kullanılarak gerçekleştirilecektir.

Bu işlem için Fortinet SSL VPN istemcisini destekleyen FortiClient uygulamasının Remote-Client sanal makinesinde bulunan Remote Access modülü kullanılacaktır.

> **Not:** FortiClient uygulaması Remote-Client sanal makinesine önceden yüklenmiştir.

---

## 1. SSL VPN Ayarlarını Yapılandırma

Bu bölümde uzak kullanıcıların SSL VPN üzerinden bağlantı kurabilmesi için gerekli ayarları yapılandıracaksınız.

### SSL VPN GUI menüsünü etkinleştirme

FortiOS 7.4 sürümünde SSL VPN Tunnel Mode ayarları ve **VPN > SSL-VPN** menüleri varsayılan olarak GUI üzerinde gizlidir.

GUI menüsünü etkinleştirmek için CLI üzerinden aşağıdaki komutları çalıştırabilirsiniz:

```bash
config system settings
    set gui-sslvpn enable
end
```

> **Not:** Bu laboratuvarın yapılandırma dosyasında SSL VPN menülerinin görüntülenmesi önceden sağlanmıştır.

### 1.1. SSL VPN Kullanıcısı Oluşturma

1. Local-FortiGate GUI arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla oturum açın.
3. **User & Authentication > User Definition** menüsüne gidin.
4. **Create New** seçeneğine tıklayın.
5. **Local User** seçeneğini belirleyin ve **Next** seçeneğine tıklayın.
6. Uzak bağlantı kuracak kullanıcı için aşağıdaki bilgileri girin:

| Alan | Değer |
|---|---|
| Username | student |
| Password | fortinet |

7. **Next** seçeneğine tıklayın.
8. Contact Information alanlarını boş bırakın.
9. Tekrar **Next** seçeneğine tıklayın.
10. **User Account Status** alanında **Enabled** seçeneğinin işaretli olduğunu doğrulayın.
11. **User Group** seçeneğini etkinleştirin.
12. **+** simgesine tıklayın.
13. Sağ taraftaki grup listesinden `SSL_VPN_USERS` grubunu seçin.
14. **Submit** seçeneğine tıklayarak kullanıcıyı oluşturun.

> **Not:** `SSL_VPN_USERS` grubu laboratuvar ortamında önceden oluşturulmuştur. Grubun ayarlarını incelemek için **User & Authentication > User Groups** menüsüne gidebilirsiniz.

### 1.2. SSL VPN Bağlantı Ayarlarını Yapılandırma

1. Local-FortiGate GUI üzerinde **VPN > SSL-VPN Settings** menüsüne gidin.
2. **Connection Settings** bölümünde aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Listen on Interface(s) | port1 |
| Listen on Port | 10443 |
| Server Certificate | Fortinet_Factory |
| Restrict Access | Allow access from any host |
| Inactive For | 3000 seconds |

3. **Tunnel Mode Client Settings** bölümünde aşağıdaki ayarı doğrulayın:

| Alan | Değer |
|---|---|
| Address Range | Automatically assign addresses |

4. **Authentication/Portal Mapping** bölümüne gidin.
5. **All Other Users/Groups** seçeneğini bulun.
6. **Edit** seçeneğine tıklayın.
7. **Portal** alanından `tunnel-access` seçeneğini belirleyin.
8. **OK** seçeneğine tıklayın.
9. Yapılandırmayı kaydetmek için **Apply** seçeneğine tıklayın.

---

## 2. Tunnel Mode İçin Routing Yapılandırma

Bu bölümde SSL VPN Tunnel Mode üzerinden erişilecek yerel ağın yönlendirme ayarlarını yapılandıracaksınız.

Tunnel Mode kullanıldığında FortiClient, VPN bağlantısı kurulduktan sonra uzak kullanıcının bilgisayarında bir veya daha fazla route oluşturur.

Bu sayede dahili ağlara gönderilen trafik otomatik olarak VPN tüneli üzerinden yönlendirilir.

### Tunnel Mode routing yapılandırmak için

1. Local-FortiGate GUI üzerinde **VPN > SSL-VPN Portals** menüsüne gidin.
2. `tunnel-access` portalını seçin.
3. **Edit** seçeneğine tıklayın.
4. **Tunnel Mode** bölümüne gidin.
5. **Routing Address Override** alanından `LOCAL_SUBNET` seçeneğini belirleyin.
6. Yapılandırmayı kaydetmek için **OK** seçeneğine tıklayın.

Bu yapılandırma sayesinde `10.0.1.0/24` yerel ağına gönderilen trafik SSL VPN tüneli üzerinden yönlendirilir.

---

## 3. SSL VPN İçin Firewall Policy Oluşturma

Bu bölümde SSL VPN bağlantısı gerçekleştiren uzak kullanıcıların yerel ağa (`10.0.1.0/24`) erişmesine izin veren bir Firewall Policy oluşturacaksınız.

### SSL VPN Firewall Policy oluşturmak için

1. Local-FortiGate GUI üzerinde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. **Create New** seçeneğine tıklayın.
3. Aşağıdaki ayarları kullanarak yeni bir güvenlik duvarı politikası oluşturun:

| Alan | Değer |
|---|---|
| Name | SSL-VPN-Access |
| Incoming Interface | SSL-VPN tunnel interface (ssl.root) |
| Outgoing Interface | port3 |
| Source Address | SSLVPN_TUNNEL_ADDR1 |
| Source User | SSL_VPN_USERS |
| Destination | LOCAL_SUBNET |
| Schedule | always |
| Service | ALL |
| Action | ACCEPT |
| Inspection Mode | Flow-based |
| NAT | Disabled |

4. Yapılandırmayı kaydetmek için **OK** seçeneğine tıklayın.

### Firewall Policy'nin çalışma mantığı

Oluşturulan politika aşağıdaki trafik akışına izin verir:

- Kaynak: SSL VPN üzerinden bağlanan uzak kullanıcılar.
- Kullanıcı grubu: `SSL_VPN_USERS`.
- Gelen arayüz: `ssl.root`.
- Giden arayüz: `port3`.
- Hedef ağ: `LOCAL_SUBNET` (`10.0.1.0/24`).
- İşlem: `ACCEPT`.

Böylece SSL VPN bağlantısı gerçekleştiren kullanıcılar yerel ağ kaynaklarına erişebilir.

---

## 4. FortiClient Üzerinde SSL VPN Bağlantısını Yapılandırma

SSL VPN Tunnel Mode bağlantısı kurmak için FortiClient kullanılacaktır.

Bu laboratuvarda FortiClient, Remote-Client sanal makinesine önceden yüklenmiştir.

### 4.1. Remote-Client Sanal Makinesine Bağlanma

1. Remote-Client sanal makinesine bağlanın.
2. Aşağıdaki kullanıcı bilgileriyle oturum açın:

| Alan | Değer |
|---|---|
| Username | Administrator |
| Password | password |

3. Masaüstünde aşağıdaki klasör yolunu takip edin:

```text
Desktop > forticlientsslvpn > 64bit
```

4. `forticlientsslvpn` uygulamasına çift tıklayarak SSL VPN istemcisini başlatın.

### 4.2. FortiClient SSL VPN Ayarlarını Yapılandırma

FortiClient SSL VPN uygulamasında aşağıdaki bağlantı bilgilerini girin:

| Alan | Değer |
|---|---|
| Server | 10.200.1.1 |
| Customize port | 10443 |
| User | student |
| Password | fortinet |

5. Bağlantıyı başlatmak için **Connect** seçeneğine tıklayın.
6. Sertifika onayı ekranında **Continue** seçeneğine tıklayarak sertifikayı kabul edin.

Beklenen sonuç:

FortiClient üzerinden SSL VPN Tunnel Mode bağlantısı başarıyla kurulmalıdır.

---

## 5. SSL VPN Tünel Bağlantısını Test Etme

Bu aşamada Remote-Client sanal makinesinden yerel ağda bulunan web sunucusuna erişerek VPN bağlantısını test edeceksiniz.

### VPN bağlantısını test etmek için

1. Remote-Client sanal makinesinde Firefox tarayıcısını açın.
2. Aşağıdaki URL adresine gidin:

```text
http://10.0.1.10
```

3. Web sunucusunun açıldığını doğrulayın.
4. Tarayıcının adres çubuğundaki IP adresini kontrol edin.

Beklenen sonuç:

`10.0.1.10` adresindeki web sunucusuna erişebilmelisiniz.

Bu erişim, Remote-Client bilgisayarının fiziksel olarak yerel ağda bulunmamasına rağmen SSL VPN tüneli üzerinden `10.0.1.0/24` ağına erişebildiğini gösterir.

---

## 6. SSL VPN Kullanıcısını İzleme ve Bağlantıyı Sonlandırma

Bu bölümde FortiGate GUI üzerinden aktif SSL VPN kullanıcılarını görüntüleyecek ve bağlantıyı sonlandıracaksınız.

### SSL VPN kullanıcısını izlemek için

1. Local-FortiGate GUI arayüzüne geri dönün.
2. **Dashboard > Network** menüsüne gidin.
3. **SSL-VPN** widget'ını görüntüleyin.

Bu ekranda SSL VPN üzerinden bağlantı gerçekleştiren kullanıcıları görebilirsiniz.

Laboratuvarda aşağıdaki bilgilerin görüntülenmesi beklenir:

| Alan | Değer |
|---|---|
| Username | student |
| Remote Host | 10.200.3.1 |

### SSL VPN oturumunu sonlandırma

1. SSL-VPN widget içerisinde `student` kullanıcısına sağ tıklayın.
2. **End Session** seçeneğini belirleyin.
3. Onay penceresinde **OK** seçeneğine tıklayın.

Beklenen sonuç:

`student` kullanıcısı artık SSL VPN monitor listesinde görüntülenmemelidir.

Bu işlem, kullanıcının aktif VPN oturumunu sonlandırır.

---

## 7. VPN Olay Kayıtlarını İnceleme (Review VPN Events)

Bu bölümde laboratuvar sırasında gerçekleştirdiğiniz SSL VPN bağlantısına ait olay kayıtlarını inceleyeceksiniz.

### VPN Events loglarını görüntülemek için

1. Local-FortiGate GUI arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla oturum açın.
3. **Log & Report > System Events** menüsüne gidin.
4. **VPN Events** widget'ını genişletin.
5. SSL VPN bağlantısına ait log kayıtlarını inceleyin.

### 7.1. Tunnel-Up Log Kaydını İnceleme

VPN Events listesinde bulunan `tunnel-up` log kaydını bulun.

Daha kolay görüntülemek için log filtrelerini kullanarak aşağıdaki filtreyi uygulayın:

| Filtre | Değer |
|---|---|
| Action | tunnel-up |

`tunnel-up` log kaydı, FortiClient üzerinden Tunnel Mode kullanılarak başarılı bir SSL VPN bağlantısı kurulduğunu gösterir.

Log detaylarında iki önemli IP adresi bulunur:

**1. Remote IP**

Uzak kullanıcının ağ geçidinin (gateway) veya internet çıkış arayüzünün IP adresidir.

**2. Tunnel IP**

FortiGate tarafından kullanıcının bilgisayarındaki `fortissl` sanal ağ adaptörüne atanan IP adresidir.

### IP adreslerinin farkı

| IP Türü | Açıklama |
|---|---|
| Remote IP | Uzak kullanıcının bağlantı kurduğu ağın çıkış IP adresi. |
| Tunnel IP | FortiGate tarafından VPN tüneli için kullanıcıya atanan sanal IP adresi. |

Bu iki IP adresi, SSL VPN bağlantısının nasıl kurulduğunu ve uzak kullanıcının hangi sanal IP üzerinden iletişim kurduğunu anlamak açısından önemlidir.

---

## 8. Laboratuvar Çalışmasını Tamamlama

Bu laboratuvar çalışmasında aşağıdaki işlemleri gerçekleştirdiniz:

1. SSL VPN kullanıcısı oluşturdunuz.
2. SSL VPN bağlantı ayarlarını yapılandırdınız.
3. Tunnel Mode için routing ayarlarını yaptınız.
4. SSL VPN Firewall Policy oluşturdunuz.
5. FortiClient üzerinden SSL VPN bağlantısı kurdunuz.
6. Yerel ağdaki web sunucusuna erişimi test ettiniz.
7. FortiGate üzerinden aktif SSL VPN kullanıcılarını izlediniz.
8. SSL VPN oturumunu sonlandırdınız.
9. VPN Events loglarını incelediniz.
10. `tunnel-up` olay kaydından Remote IP ve Tunnel IP bilgilerini öğrendiniz.

---

## 9. Önemli Kavramlar – Hızlı Referans

| Kavram | Açıklama |
|---|---|
| SSL VPN | Uzak kullanıcıların şifreli bağlantı üzerinden kurumsal ağa erişmesini sağlar. |
| Tunnel Mode | Uzak kullanıcının bilgisayarında VPN tüneli oluşturarak ağ trafiğini yönlendirir. |
| FortiClient | Fortinet VPN bağlantılarını gerçekleştiren istemci uygulaması. |
| ssl.root | FortiGate üzerindeki SSL VPN sanal tünel arayüzü. |
| tunnel-access | SSL VPN Tunnel Mode erişimi için kullanılan portal. |
| LOCAL_SUBNET | Yerel ağ adres nesnesi. |
| SSL_VPN_USERS | SSL VPN bağlantısı yapmasına izin verilen kullanıcı grubu. |
| SSLVPN_TUNNEL_ADDR1 | SSL VPN kullanıcıları için kullanılan adres nesnesi. |
| Remote IP | Uzak kullanıcının ağ çıkış IP adresi. |
| Tunnel IP | VPN bağlantısı sırasında kullanıcıya atanan sanal IP adresi. |
| tunnel-up | SSL VPN tünelinin başarıyla kurulduğunu gösteren olay. |
| End Session | Aktif VPN oturumunu sonlandırır. |

---

## 10. Laboratuvar Sonu Kontrol Listesi

- [ ] SSL VPN GUI menüsü kontrol edildi.
- [ ] `student` isimli yerel kullanıcı oluşturuldu.
- [ ] Kullanıcı `SSL_VPN_USERS` grubuna eklendi.
- [ ] SSL VPN dinleme arayüzü `port1` olarak ayarlandı.
- [ ] SSL VPN portu `10443` olarak ayarlandı.
- [ ] Server Certificate `Fortinet_Factory` olarak seçildi.
- [ ] Inactive For değeri `3000 seconds` olarak ayarlandı.
- [ ] Portal `tunnel-access` olarak belirlendi.
- [ ] Routing Address Override `LOCAL_SUBNET` olarak ayarlandı.
- [ ] SSL-VPN-Access Firewall Policy oluşturuldu.
- [ ] Kaynak adres `SSLVPN_TUNNEL_ADDR1` olarak seçildi.
- [ ] Kaynak kullanıcı grubu `SSL_VPN_USERS` olarak seçildi.
- [ ] Hedef ağ `LOCAL_SUBNET` olarak seçildi.
- [ ] FortiClient bağlantı adresi `10.200.1.1` olarak ayarlandı.
- [ ] FortiClient bağlantı portu `10443` olarak ayarlandı.
- [ ] `student` kullanıcısıyla SSL VPN bağlantısı kuruldu.
- [ ] `10.0.1.10` web sunucusuna erişim sağlandı.
- [ ] FortiGate üzerinden aktif VPN kullanıcısı görüntülendi.
- [ ] SSL VPN oturumu sonlandırıldı.
- [ ] VPN Events logları incelendi.
- [ ] `tunnel-up` kaydı doğrulandı.
- [ ] Remote IP ve Tunnel IP bilgileri incelendi.

**LAB-10 – Configuring SSL VPN Tunnel Mode başarıyla tamamlandı.**
