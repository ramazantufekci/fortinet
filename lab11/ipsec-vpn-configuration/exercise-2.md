# LAB-11 – Exercise 2: İki FortiGate Cihazı Arasında Statik IPsec VPN Yapılandırması

Bu laboratuvar çalışmasında Local-FortiGate ve Remote-FortiGate arasında statik IPsec VPN bağlantısı yapılandırılacaktır.

Ayrıca VPN trafiğinin doğru şekilde yönlendirilmesi için Local-FortiGate üzerinde statik route oluşturulacaktır.

**Cihaz rolleri:**

- **Local-FortiGate:** Statik IPsec VPN cihazı
- **Remote-FortiGate:** Uzak VPN cihazı

**Ağ bilgileri:**

| Açıklama | Ağ / IP |
|---|---|
| HQ (Merkez) ağı | 10.0.1.0/24 |
| Branch (Şube) ağı | 10.0.2.0/24 |
| Local-FortiGate WAN IP | 10.200.1.1 |
| Remote-FortiGate WAN IP | 10.200.3.1 |
| Local VPN arayüzü | ToRemote |
| Local LAN arayüzü | port3 |
| VPN WAN arayüzü | port1 |

---

## 1. Local-FortiGate Yapılandırma Dosyasını Geri Yükleme

Laboratuvara başlamadan önce Local-FortiGate üzerinde doğru yapılandırma dosyasının geri yüklenmesi gerekmektedir.

**Dikkat:** Local-FortiGate üzerinde yanlış yapılandırma dosyasının geri yüklenmesi laboratuvar çalışmasının başarısız olmasına neden olabilir.

### 1.1. Yapılandırma Dosyasını Geri Yükleme Adımları

1. Local-FortiGate yönetim arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla giriş yapın.
3. Sağ üst köşedeki **admin** menüsüne tıklayın.
4. **Configuration > Revisions** menüsüne gidin.
5. `+` simgesine tıklayarak yapılandırma geçmişini genişletin.
6. Açıklaması `local-ipsec-vpn` olan yapılandırmayı bulun.
7. İlgili yapılandırmayı seçin ve **Revert** butonuna tıklayın.
8. Yeniden başlatma işlemini onaylamak için **OK** butonuna tıklayın.

Local-FortiGate yeniden başlatıldıktan sonra laboratuvarın sonraki adımlarına geçebilirsiniz.

---

## 2. Local-FortiGate Üzerinde Phase 1 ve Phase 2 Yapılandırması

Bu bölümde Local-FortiGate üzerinde statik IPsec VPN bağlantısı oluşturulacaktır.

### 2.1. IPsec Tüneli Oluşturma

1. Local-FortiGate yönetim arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla giriş yapın.
3. **VPN > IPsec Tunnels** menüsüne gidin.
4. **Create New > IPsec Tunnel** seçeneğine tıklayın.
5. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Name | ToRemote |
| Template type | Custom |

6. **Next** butonuna tıklayın.

### 2.2. Network Ayarları

| Alan | Değer |
|---|---|
| Remote Gateway | Static IP Address |
| IP Address | 10.200.3.1 |
| Interface | port1 |
| Dead Peer Detection | On Idle |

### 2.3. Authentication Ayarları

| Alan | Değer |
|---|---|
| Method | Pre-shared Key |
| Pre-shared Key | fortinet |
| Version | 1 |
| Mode | Aggressive |
| Accept Types | Any peer ID |

### 2.4. Phase 2 Selectors Ayarları

| Alan | Değer |
|---|---|
| Local Address | 10.0.1.0/24 |
| Remote Address | 10.0.2.0/24 |

Diğer ayarları varsayılan değerlerinde bırakın.

**OK** butonuna tıklayarak VPN yapılandırmasını kaydedin.

---

## 3. Local-FortiGate Üzerinde Statik Route Oluşturma

VPN trafiğinin doğru şekilde yönlendirilebilmesi için Local-FortiGate üzerinde bir statik route oluşturulacaktır.

### 3.1. Statik Route Tanımlama

1. Local-FortiGate yönetim arayüzünde **Network > Static Routes** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Destination | 10.0.2.0/24 |
| Interface | ToRemote |

4. **OK** butonuna tıklayarak yapılandırmayı kaydedin.

Bu işlem sayesinde Local-FortiGate, `10.0.2.0/24` şube ağına giden trafiği `ToRemote` IPsec VPN tüneline yönlendirecektir.

---

## 4. Local-FortiGate Üzerinde Firewall Policy Oluşturma

VPN üzerinden geçen trafiğin iletilebilmesi için `port3` ile `ToRemote` VPN arayüzü arasında iki yönlü firewall policy oluşturulacaktır.

### 4.1. Remote_out Politikası Oluşturma

1. **Policy & Objects > Firewall Policy** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Name | Remote_out |
| Incoming Interface | port3 |
| Outgoing Interface | ToRemote |
| Source | HQ_SUBNET |
| Destination | BRANCH_SUBNET |
| Schedule | always |
| Service | ALL |
| Action | ACCEPT |

4. **Firewall/Network Options** bölümüne gidin.
5. NAT seçeneğini devre dışı bırakın.
6. **OK** butonuna tıklayın.

### 4.2. Reverse Policy Oluşturma

İkinci firewall policy'yi elle oluşturmak yerine ilk politikadan ters yönlü bir politika oluşturacağız.

1. Oluşturduğunuz `Remote_out` politikasına sağ tıklayın.
2. **Create reverse policy** seçeneğine tıklayın.

Yeni bir ters yönlü politika oluşturulacaktır.

**Not:** Oluşturulan reverse policy varsayılan olarak devre dışı durumdadır.

### 4.3. Remote_in Politikasını Düzenleme

1. Yeni oluşturulan ters yönlü politikayı seçin.
2. **Edit** butonuna tıklayın.
3. Name alanına `Remote_in` yazın.
4. Aşağıdaki ayarları kontrol edin.

| Alan | Değer |
|---|---|
| Name | Remote_in |
| Incoming Interface | ToRemote |
| Outgoing Interface | port3 |
| Source | BRANCH_SUBNET |
| Destination | HQ_SUBNET |
| Schedule | always |
| Service | ALL |
| Action | ACCEPT |

5. **Firewall/Network Options** bölümüne gidin.
6. NAT seçeneğini devre dışı bırakın.
7. **Enable this policy** seçeneğini etkinleştirin.
8. **OK** butonuna tıklayarak kaydedin.

Artık Local-FortiGate üzerinde VPN trafiğinin iki yönü için gerekli firewall policy yapılandırılmıştır.

---

## 5. VPN Bağlantısını Test Etme ve İzleme

Her iki FortiGate cihazında gerekli yapılandırmalar tamamlandıktan sonra VPN bağlantısının çalıştığı kontrol edilecektir.

### 5.1. VPN Tünel Durumunu Kontrol Etme

1. Local-FortiGate yönetim arayüzüne giriş yapın.
2. **Dashboard > Network > IPsec** menüsüne gidin.
3. **Custom** bölümünün yanındaki `+` simgesine tıklayın.
4. `ToRemote` VPN bağlantısının başlangıçta DOWN durumda olduğunu göreceksiniz.

### 5.2. VPN Tünelini Manuel Olarak Başlatma

1. `ToRemote` VPN bağlantısına sağ tıklayın.
2. **Bring Up > All Phase 2 Selectors** seçeneğine tıklayın.

VPN bağlantısı başarıyla kurulduğunda:

- VPN tünelinin durumu UP olacaktır.
- Name sütununda yeşil yukarı ok simgesi görünecektir.

Sağ üst köşedeki yenileme butonuna tıklayarak bağlantı durumunu güncelleyin.

### 5.3. Ping Testi Gerçekleştirme

Remote-Client sanal makinesinde terminal penceresini açın.

Local-Client sanal makinesinin IP adresine ping gönderin:

```bash
ping 10.0.1.10
```

Ping yanıtlarının başarılı olması, iki ağ arasında VPN üzerinden iletişim kurulabildiğini gösterir.

### 5.4. VPN Trafik Sayaçlarını Kontrol Etme

1. Local-FortiGate yönetim arayüzüne dönün.
2. **Dashboard > Network > IPsec** menüsünü açın.
3. Sağ üst köşedeki yenileme butonuna birkaç kez tıklayın.
4. Aşağıdaki sayaçları kontrol edin:
   - Incoming Data
   - Outgoing Data

Sayaçların zaman içerisinde artması, `10.0.1.10` ve `10.0.2.10` IP adresleri arasındaki trafiğin IPsec VPN üzerinden şifrelenerek iletildiğini gösterir.

### 5.5. Ping İşlemini Sonlandırma

Remote-Client sanal makinesindeki terminal penceresine dönün.

Ping işlemini durdurmak için:

```text
Ctrl + C
```

tuş kombinasyonunu kullanın.

---

## 6. Laboratuvar Kontrol Listesi

Aşağıdaki maddeleri kontrol ederek laboratuvar çalışmasının tamamlandığını doğrulayabilirsiniz.

- [ ] Local-FortiGate üzerinde `local-ipsec-vpn` yapılandırması geri yüklendi.
- [ ] Local-FortiGate üzerinde `ToRemote` IPsec VPN tüneli oluşturuldu.
- [ ] Phase 1 yapılandırması tamamlandı.
- [ ] Phase 2 Selectors yapılandırması tamamlandı.
- [ ] Local-FortiGate üzerinde `10.0.2.0/24` ağı için statik route oluşturuldu.
- [ ] `Remote_out` firewall policy oluşturuldu.
- [ ] `Remote_in` reverse policy oluşturuldu ve etkinleştirildi.
- [ ] NAT her iki firewall policy için devre dışı bırakıldı.
- [ ] VPN tüneli başarıyla başlatıldı.
- [ ] Remote-Client üzerinden Local-Client cihazına ping testi gerçekleştirildi.
- [ ] Incoming Data ve Outgoing Data sayaçları kontrol edildi.
- [ ] VPN üzerinden trafik iletimi doğrulandı.

---

## 7. Laboratuvar Sonucu

Bu laboratuvar çalışmasında iki FortiGate cihazı arasında statik IPsec VPN bağlantısı yapılandırılmıştır.

Local-FortiGate üzerinde Phase 1 ve Phase 2 ayarları oluşturulmuş, VPN trafiği için statik route tanımlanmış ve iki yönlü firewall policy yapılandırması gerçekleştirilmiştir.

Son olarak VPN bağlantısı başlatılmış, ping testi uygulanmış ve IPsec trafik sayaçları kontrol edilerek bağlantının başarılı olduğu doğrulanmıştır.

**LAB-11 – Exercise 2 tamamlandı.**
