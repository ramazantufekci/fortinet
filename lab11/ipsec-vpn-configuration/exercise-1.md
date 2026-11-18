# LAB-11: İki FortiGate Cihazı Arasında Dial-Up IPsec VPN Yapılandırması

Bu laboratuvar çalışmasında Local-FortiGate ve Remote-FortiGate arasında Dial-Up IPsec VPN yapılandırılacaktır.

- **Local-FortiGate:** Dial-up VPN sunucusu (Server)
- **Remote-FortiGate:** Dial-up VPN istemcisi (Client)

Amaç, iki farklı ağın IPsec VPN tüneli üzerinden güvenli şekilde haberleşmesini sağlamaktır.

## 1. Local-FortiGate Üzerinde Phase 1 ve Phase 2 Yapılandırması

Bu bölümde Local-FortiGate cihazı üzerinde IPsec VPN için Phase 1 ve Phase 2 ayarları oluşturulacaktır.

### 1.1. IPsec Tüneli Oluşturma

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

### 1.2. Network Ayarları

| Alan | Değer |
|---|---|
| Remote Gateway | Dialup User |
| Interface | port1 |
| Dead Peer Detection | On Idle |

### 1.3. Authentication Ayarları

| Alan | Değer |
|---|---|
| Method | Pre-shared Key |
| Pre-shared Key | fortinet |
| Version | 1 |
| Mode | Aggressive |
| Accept Types | Specific peer ID |
| Peer ID | Remote-FortiGate |

**Önemli:** Peer ID tanımlamak, birden fazla dial-up VPN tünelinin bulunduğu ortamlarda istemcilerin doğru VPN tüneline bağlanmasını sağlar.

Peer ID büyük/küçük harfe duyarlıdır.

### 1.4. Phase 2 Selectors Ayarları

| Alan | Değer |
|---|---|
| Local Address | 10.0.1.0/24 |

Diğer ayarları varsayılan değerlerinde bırakın.

**OK** butonuna tıklayarak yapılandırmayı kaydedin.

### 1.5. Statik Route Hakkında

Local-FortiGate üzerinde statik route eklenmesine gerek yoktur.

Bunun nedeni, dial-up VPN sunucusunun tünel bağlantısı kurulduğunda uzak istemcinin ağına ait rotayı dinamik olarak oluşturmasıdır.

Bu laboratuvarda Local-FortiGate, Remote-FortiGate bağlantısı kurulduğunda `10.0.2.0/24` ağını dinamik olarak öğrenir.

Remote Address alanında `0.0.0.0/0` kullanılması, farklı uzak ağlara sahip birden fazla istemcinin aynı Phase 2 yapılandırmasını kullanabilmesini sağlar.

---

## 2. Local-FortiGate Üzerinde Firewall Policy Oluşturma

VPN üzerinden geçen trafiğin güvenli şekilde iletilebilmesi için iki farklı firewall policy oluşturulacaktır.

Bu politikalar `port3` ile `ToRemote` VPN arayüzü arasında tanımlanacaktır.

### 2.1. Remote_out Politikası

1. **Policy & Objects > Firewall Policy** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları girin.

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

4. Firewall/Network Options bölümüne gidin.
5. **NAT** seçeneğini devre dışı bırakın.
6. **OK** butonuna tıklayın.

### 2.2. Remote_in Politikası

Yeni bir firewall policy oluşturun.

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

1. Firewall/Network Options bölümüne gidin.
2. **NAT** seçeneğini devre dışı bırakın.
3. **OK** butonuna tıklayın.

**Not:** VPN üzerinden iletişim kurulabilmesi için her iki trafik yönünde de uygun firewall politikalarının bulunması gerekir.

---

## 3. Remote-FortiGate Üzerinde Phase 1 ve Phase 2 Yapılandırması

Bu bölümde Remote-FortiGate cihazı üzerinde VPN istemci yapılandırması gerçekleştirilecektir.

### 3.1. IPsec Tüneli Oluşturma

1. Remote-FortiGate yönetim arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla giriş yapın.
3. **VPN > IPsec Tunnels** menüsüne gidin.
4. **Create New > IPsec Tunnel** seçeneğine tıklayın.
5. Aşağıdaki ayarları girin.

| Alan | Değer |
|---|---|
| Name | ToLocal |
| Template type | Custom |

6. **Next** butonuna tıklayın.

### 3.2. Network Ayarları

| Alan | Değer |
|---|---|
| Remote Gateway | Static IP Address |
| IP Address | 10.200.1.1 |
| Interface | port4 |
| Dead Peer Detection | On Idle |

### 3.3. Authentication Ayarları

| Alan | Değer |
|---|---|
| Method | Pre-shared Key |
| Pre-shared Key | fortinet |
| Version | 1 |
| Mode | Aggressive |
| Accept Types | Any peer ID |

### 3.4. Phase 1 Proposal Ayarları

Local ID alanına aşağıdaki değeri girin.

| Alan | Değer |
|---|---|
| Local ID | Remote-FortiGate |

**Önemli:** Remote-FortiGate üzerindeki Local ID değeri, Local-FortiGate üzerinde tanımlanan Peer ID ile aynı olmalıdır.

Peer ID ve Local ID değerleri büyük/küçük harfe duyarlıdır.

### 3.5. Phase 2 Selectors Ayarları

| Alan | Değer |
|---|---|
| Local Address | 10.0.2.0/24 |
| Remote Address | 10.0.1.0/24 |

Diğer ayarları varsayılan değerlerinde bırakın.

**OK** butonuna tıklayarak VPN yapılandırmasını kaydedin.

**Not:** Dial-up IPsec VPN yapılandırmasında iki cihazın Local Address ve Remote Address değerleri birbirinin aynısı olmak zorunda değildir. Ancak ağların doğru yönlerde tanımlanması gerekir.

---

## 4. Remote-FortiGate Üzerinde Statik Route Oluşturma

Remote-FortiGate, Local-FortiGate tarafındaki ağa erişebilmek için statik route kullanacaktır.

Local-FortiGate üzerinde bu işlem dinamik olarak gerçekleştirildiğinden ayrıca statik route eklenmesine gerek yoktur.

### 4.1. Statik Route Tanımlama

1. **Network > Static Routes** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları girin.

| Alan | Değer |
|---|---|
| Destination | 10.0.1.0/24 |
| Interface | ToLocal |

4. **OK** butonuna tıklayarak kaydedin.

Bu işlem sayesinde Remote-FortiGate, `10.0.1.0/24` ağına giden trafiği `ToLocal` IPsec VPN tüneline yönlendirecektir.

---

## 5. Remote-FortiGate Üzerinde Firewall Policy Oluşturma

VPN trafiğinin iki yönlü iletilebilmesi için Remote-FortiGate üzerinde iki firewall policy oluşturulacaktır.

Bu politikalar `port6` ile `ToLocal` VPN arayüzü arasında tanımlanacaktır.

### 5.1. Local_out Politikası

1. **Policy & Objects > Firewall Policy** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Name | Local_out |
| Incoming Interface | port6 |
| Outgoing Interface | ToLocal |
| Source | BRANCH_SUBNET |
| Destination | HQ_SUBNET |
| Schedule | always |
| Service | ALL |
| Action | ACCEPT |

4. Firewall/Network Options bölümünde NAT seçeneğini devre dışı bırakın.
5. **OK** butonuna tıklayın.

### 5.2. Local_in Politikası

Yeni bir firewall policy oluşturun.

| Alan | Değer |
|---|---|
| Name | Local_in |
| Incoming Interface | ToLocal |
| Outgoing Interface | port6 |
| Source | HQ_SUBNET |
| Destination | BRANCH_SUBNET |
| Schedule | always |
| Service | ALL |
| Action | ACCEPT |

1. Firewall/Network Options bölümüne gidin.
2. NAT seçeneğini devre dışı bırakın.
3. **OK** butonuna tıklayın.

---

## 6. VPN Bağlantısını Test Etme ve İzleme

Her iki FortiGate cihazında da yapılandırma tamamlandıktan sonra VPN bağlantısı test edilecektir.

### 6.1. VPN Tünelini Kontrol Etme

1. Remote-FortiGate yönetim arayüzüne giriş yapın.
2. **Dashboard > Network > IPsec** menüsüne gidin.
3. **Custom** bölümünün yanındaki `+` simgesine tıklayarak VPN tünellerini genişletin.
4. `ToLocal` VPN bağlantısının başlangıçta DOWN durumda olduğunu göreceksiniz.

### 6.2. VPN Tünelini Manuel Olarak Başlatma

1. `ToLocal` VPN bağlantısına sağ tıklayın.
2. **Bring Up > All Phase 2 Selectors** seçeneğini seçin.

Bağlantı başarılı olduğunda VPN tünelinin yanında yeşil yukarı ok simgesi görünecektir.

### 6.3. Ping Testi Gerçekleştirme

Remote-Client sanal makinesinde terminal açın.

Local-Client sanal makinesinin IP adresine ping gönderin:

```bash
ping 10.0.1.10
```

Ping işlemi başarılı olduğunda iki ağ arasında VPN üzerinden iletişim kurulabildiği doğrulanmış olur.

### 6.4. VPN Trafik Sayaçlarını Kontrol Etme

1. Remote-FortiGate arayüzüne geri dönün.
2. **Dashboard > Network > IPsec** menüsünü açın.
3. Sağ üst köşedeki yenileme simgesine birkaç kez tıklayın.
4. Incoming Data ve Outgoing Data sayaçlarını kontrol edin.

Sayaçların artması, iki ağ arasındaki trafiğin IPsec tüneli üzerinden şifrelenerek iletildiğini gösterir.

### 6.5. Local-FortiGate Üzerindeki Dinamik Route Kontrolü

1. Local-FortiGate yönetim arayüzüne giriş yapın.
2. **Dashboard > Network > Static & Dynamic Routing** bölümüne gidin.
3. VPN bağlantısı kurulduktan sonra otomatik eklenen rotayı bulun.
4. Route detaylarını görüntüleyin.
5. Gateway IP sütunundaki IP adresini inceleyin.

Bu işlem, Local-FortiGate cihazının uzak ağ rotasını dinamik olarak öğrendiğini doğrular.

### 6.6. Ping İşlemini Sonlandırma

Remote-Client terminaline dönün ve ping işlemini durdurmak için aşağıdaki tuş kombinasyonunu kullanın:

```text
Ctrl + C
```

---

## 7. Stop and Think! – Kontrol Sorusu

**Soru:** VPN tünelini oluşturduktan sonra her zaman manuel olarak başlatmak gerekir mi?

**Cevap:** Hayır.

Mevcut yapılandırmada VPN tüneli aşağıdaki iki durumda aktif hale gelebilir:

- VPN bağlantısı manuel olarak başlatıldığında.
- VPN üzerinden yönlendirilmesi gereken bir trafik oluştuğunda.

Bu laboratuvarda başlangıçta `10.0.2.0/24` ve `10.0.1.0/24` ağları arasında trafik oluşturulmadığı için VPN tüneli DOWN durumundadır.

Ancak gerekli ağlar arasında trafik oluştuğunda VPN tüneli otomatik olarak kurulabilir.

**Önemli:** Dial-up VPN bağlantısını yalnızca Remote-FortiGate başlatabilir. Çünkü Remote-FortiGate dial-up istemcisi, Local-FortiGate ise dial-up sunucusudur.

---

## 8. Laboratuvar Sonuçları

Bu laboratuvar çalışmasının sonunda aşağıdaki işlemler başarıyla gerçekleştirilmiş olmalıdır:

- [ ] Local-FortiGate üzerinde Dial-Up IPsec VPN oluşturuldu.
- [ ] Local-FortiGate Phase 1 yapılandırması tamamlandı.
- [ ] Local-FortiGate Phase 2 yapılandırması tamamlandı.
- [ ] Local-FortiGate üzerinde iki yönlü firewall policy oluşturuldu.
- [ ] Remote-FortiGate üzerinde IPsec VPN istemci yapılandırması tamamlandı.
- [ ] Remote-FortiGate Phase 1 ve Phase 2 yapılandırmaları tamamlandı.
- [ ] Remote-FortiGate üzerinde statik route oluşturuldu.
- [ ] Remote-FortiGate üzerinde iki yönlü firewall policy oluşturuldu.
- [ ] VPN tüneli başarıyla başlatıldı.
- [ ] `10.0.2.0/24` ve `10.0.1.0/24` ağları arasında ping testi gerçekleştirildi.
- [ ] IPsec trafik sayaçları kontrol edildi.
- [ ] Local-FortiGate üzerindeki dinamik route doğrulandı.

**LAB-11 tamamlandı.**

İki FortiGate cihazı arasında Dial-Up IPsec VPN bağlantısı kurulmuş ve iki farklı yerel ağ arasında güvenli iletişim sağlanmıştır.
