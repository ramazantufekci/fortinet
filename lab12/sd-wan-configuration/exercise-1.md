# FortiGate – SD-WAN Yapılandırması

## Laboratuvarın Amacı

Bu laboratuvar çalışmasında FortiGate GUI üzerinden temel bir **DIA (Direct Internet Access)** yapılandırması gerçekleştirilecektir.

Local-FortiGate üzerinde `port1` ve `port2` arayüzleri için bir SD-WAN Zone oluşturulacak ve kritik / kritik olmayan internet uygulamalarının trafiğini yönlendirmek amacıyla SD-WAN kuralları yapılandırılacaktır.

Bu çalışma kapsamında:

- `port1` ve `port2` arayüzleri SD-WAN üyesi olarak tanımlanacaktır.
- `underlay` isimli SD-WAN Zone oluşturulacaktır.
- Performance SLA yapılandırılacaktır.
- Kritik ve kritik olmayan uygulamalar için SD-WAN kuralları oluşturulacaktır.
- SD-WAN trafiği için statik route ve firewall policy tanımlanacaktır.
- Yönlendirme tablosu kontrol edilecektir.

---

# 1. Interface References (Arayüz Referanslarını Kaldırma)

`port1` ve `port2` arayüzlerini SD-WAN üyesi olarak ekleyebilmek için öncelikle bu arayüzleri kullanan mevcut firewall policy tanımlarının kaldırılması gerekir.

### Uzmanlık Görevi

Local-FortiGate GUI üzerinden (`admin/password`) `port1` ve `port2` arayüzlerini referans alan tüm firewall policy kayıtlarını kaldırın.

Yardım almak veya işleminizi doğrulamak için aşağıdaki adımları uygulayabilirsiniz.

### 1.1. Firewall Policy Silme

1. Local-FortiGate GUI arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla giriş yapın.
3. **Policy & Objects > Firewall Policy** menüsüne gidin.
4. `Full_Access` isimli firewall policy kaydını seçin.
5. **Delete** butonuna tıklayın.
6. Silme işlemini onaylamak için **OK** butonuna tıklayın.

Bu işlemden sonra `port1` ve `port2` arayüzleri SD-WAN üyesi olarak tanımlanabilir.

---

# 2. DIA İçin SD-WAN Zone ve Üyelerin Yapılandırılması

Bu bölümde Underlay Zone oluşturulacak ve `port1` ile `port2` arayüzleri bu zone içerisine eklenecektir.

### Uzmanlık Görevi

Local-FortiGate üzerinde aşağıdaki yapılandırmaları gerçekleştirin:

- `underlay` isimli bir SD-WAN Zone oluşturun.
- `port1` arayüzünü `underlay` zone içerisine ekleyin.
- `port2` arayüzünü `underlay` zone içerisine ekleyin.

Üye arayüzlerin gateway bilgileri:

| Interface | Gateway |
|---|---|
| port1 | 10.200.1.254 |
| port2 | 10.200.2.254 |

### 2.1. SD-WAN Zone Oluşturma

1. Local-FortiGate GUI üzerinde **Network > SD-WAN > SD-WAN Zones** menüsüne gidin.
2. **Create New > SD-WAN Zone** seçeneğine tıklayın.
3. Name alanına `underlay` yazın.
4. **OK** butonuna tıklayın.

### 2.2. port1 Arayüzünü SD-WAN Üyesi Olarak Ekleme

1. **Create New > SD-WAN Member** seçeneğine tıklayın.
2. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Interface | port1 |
| SD-WAN Zone | underlay |
| Gateway | 10.200.1.254 |
| Status | Enabled |

3. **OK** butonuna tıklayın.

### 2.3. port2 Arayüzünü SD-WAN Üyesi Olarak Ekleme

1. Tekrar **Create New > SD-WAN Member** seçeneğine tıklayın.
2. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Interface | port2 |
| SD-WAN Zone | underlay |
| Gateway | 10.200.2.254 |
| Status | Enabled |

3. **OK** butonuna tıklayın.

### 2.4. SD-WAN Zone Üyelerini Kontrol Etme

1. **SD-WAN Zones** sekmesine gidin.
2. `underlay` zone'unu genişletin.

Aşağıdaki arayüzlerin zone içerisinde bulunduğunu doğrulayın:

- port1
- port2

**Not:** Varsayılan SD-WAN Zone olan `virtual-wan-link` içerisinde henüz üye bulunmadığı için kırmızı simgeyle gösterilir. Bu durum, ilgili zone'un boş olduğunu ifade eder.

---

# 3. Performance SLA Yapılandırması

Bu bölümde `port1` ve `port2` arayüzlerinin bağlantı kalitesini ve erişilebilirliğini izlemek için Performance SLA oluşturulacaktır.

Performance SLA sayesinde FortiGate, belirlenen hedef sunuculara erişimi kontrol ederek bağlantıların durumunu takip eder.

### 3.1. Performance SLA Oluşturma

1. Local-FortiGate GUI üzerinde **Network > SD-WAN** menüsüne gidin.
2. **Performance SLAs** sekmesini açın.
3. **Create New** butonuna tıklayın.
4. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Name | Level3_DNS |
| Server | 4.2.2.1 |

5. Server alanındaki `+` simgesine tıklayın.
6. İkinci DNS sunucusunu ekleyin:

```text
4.2.2.2
```

7. Participants alanında **Specify** seçeneğini işaretleyin.
8. Aşağıdaki arayüzleri seçin:
   - port1
   - port2
9. **OK** butonuna tıklayarak ayarları kaydedin.

### 3.2. Performance SLA Durumunu Kontrol Etme

1. **Network > SD-WAN > Performance SLAs** sekmesine tekrar gidin.
2. Sayfayı yenileyin.
3. `port1` ve `port2` arayüzlerinin UP durumda olduğunu doğrulayın.

FortiGate, her iki arayüz üzerinden de tanımlanan algılama sunucularına erişebildiğinde aşağıdaki performans değerlerini gösterecektir:

- Packet Loss (Paket Kaybı)
- Latency (Gecikme)
- Jitter (Gecikme Değişkenliği)

Bu değerlerin yanında yeşil ok simgesi görünmesi, ilgili bağlantı ölçümünün başarılı olduğunu gösterir.

### 3.3. Routing Table Kontrolü

1. **Dashboard > Network** menüsüne gidin.
2. **Static & Dynamic Routing** widget'ını görüntüleyin.
3. Mevcut yönlendirme tablosunu inceleyin.

### Stop and Think! – Kontrol Sorusu

**Soru:** Yönlendirme tablosunda `4.2.2.1` ve `4.2.2.2` adreslerine ait route bulunmamasına rağmen Performance SLA neden `port1` ve `port2` bağlantılarını UP olarak gösteriyor?

**Cevap:**

FortiOS, sağlık kontrolü paketlerini doğru şekilde yönlendirebilmek için üye arayüzlerin gateway bilgilerini kullanarak Forwarding Information Base (FIB) içerisine özel rotalar ekler.

Bu rotalar standart routing table içerisinde görüntülenmez.

Ancak aşağıdaki CLI komutuyla bu özel rotalar incelenebilir:

```bash
get router info kernel
```

---

# 4. SD-WAN Rules (SD-WAN Kurallarının Yapılandırılması)

Bu bölümde iki farklı SD-WAN kuralı oluşturulacaktır.

- **Critical-DIA:** Kritik uygulamaların internet trafiğini yönlendirecek.
- **Non-Critical-DIA:** Kritik olmayan uygulamaların internet trafiğini yönlendirecek.

Her iki kuralda da Manual mode kullanılacaktır.

### 4.1. Application Detection Özelliği

Varsayılan olarak SD-WAN Rules içerisindeki Application Detection özelliği GUI üzerinde görünmeyebilir.

Bu laboratuvarda ilgili özellik Local-FortiGate üzerinde etkinleştirilmiştir.

Özelliği CLI üzerinden etkinleştirmek için aşağıdaki komutlar kullanılabilir:

```bash
config system global
    set gui-app-detection-sdwan enable
end
```

### 4.2. Critical-DIA Kuralını Oluşturma

1. **Network > SD-WAN** menüsüne gidin.
2. **SD-WAN Rules** sekmesini açın.
3. **Create New** butonuna tıklayın.
4. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Name | Critical-DIA |
| Source address | LOCAL_SUBNET |
| Internet service | Slack-Slack, Dropbox-Web |
| Application | Bloomberg |
| Outgoing Interfaces | Manual |
| Interface preference | port1, ardından port2 |

5. **OK** butonuna tıklayarak kuralı kaydedin.

**Önemli Bilgi: Internet Service ve Application Kullanımı**

Aynı SD-WAN kuralı içerisinde hem Internet Service hem de Application tabanlı trafik tespiti kullanılabilir.

**Internet Service:**

FortiGate, uygulamalara ait IP adreslerini ve protokolleri Internet Service Database (ISDB) üzerinden belirler.

FortiGuard sunucuları bu listeyi düzenli olarak günceller ve FortiGate cihazına yükler.

**Application:**

FortiGate, oturumun başlangıcında gerçekleşen ilk paketleri analiz ederek uygulamayı tespit eder.

Bu sayede belirli uygulamaların trafiği farklı SD-WAN üyeleri üzerinden yönlendirilebilir.

### 4.3. Non-Critical-DIA Kuralını Oluşturma

Kritik olmayan uygulamalar için ikinci bir SD-WAN kuralı oluşturulacaktır.

1. **SD-WAN Rules** sekmesinde **Create New** butonuna tıklayın.
2. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Name | Non-Critical-DIA |
| Source address | LOCAL_SUBNET |

**Application** alanında:

- Apps seçeneğini açın.
- `Addicting.Games` uygulamasını seçin.
- Category seçeneğini açın.
- `Social.Media` kategorisini seçin.

**Outgoing Interfaces** alanında:

| Alan | Değer |
|---|---|
| Outgoing Interfaces | Manual |
| Interface preference | port2 |

3. **OK** butonuna tıklayarak kuralı kaydedin.

### 4.4. Implicit SD-WAN Rule Yapılandırması

1. **SD-WAN Rules** sekmesinde bulunan implicit `sd-wan` kuralına çift tıklayın.
2. Load Balancing Algorithm alanında aşağıdaki algoritmayı seçin:

```text
Source-Destination IP
```

3. **OK** butonuna tıklayarak ayarları kaydedin.

### 4.5. SD-WAN Kurallarının Kontrolü

SD-WAN Rules ekranında aşağıdaki kuralların oluşturulduğunu doğrulayın:

| Kural | Trafik | Tercih Edilen Arayüz |
|---|---|---|
| Critical-DIA | Slack, Dropbox, Bloomberg | port1, ardından port2 |
| Non-Critical-DIA | Addicting.Games, Social.Media | port2 |
| Implicit sd-wan | Diğer eşleşmeyen trafik | Source-Destination IP yük dengeleme |

### Stop and Think! – Kontrol Sorusu

**Soru:** Birinci kuralda neden `port1`, ikinci kuralda neden `port2` tercih edilen üye olarak kullanılıyor?

**Cevap:**

- **Critical-DIA:** Üye listesinde ilk sırada `port1` bulunduğu ve bağlantı aktif olduğu için öncelikli olarak `port1` kullanılır. `port1` kullanılamaz hale gelirse `port2` devreye girebilir.
- **Non-Critical-DIA:** Bu kuralda yalnızca `port2` tanımlandığı ve bağlantı aktif olduğu için trafik `port2` üzerinden yönlendirilir.

---

# 5. Statik Route ve Firewall Policy Yapılandırması

Bu bölümde SD-WAN trafiğinin yönlendirilmesi ve internete erişimin sağlanması için statik route ve firewall policy oluşturulacaktır.

Her iki yapılandırmada da `underlay` SD-WAN Zone kullanılacaktır.

## 5.1. SD-WAN İçin Statik Route Oluşturma

1. **Network > Static Routes** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları yapılandırın.

| Alan | Değer |
|---|---|
| Destination | 0.0.0.0/0.0.0.0 |
| Interface | underlay |

4. **OK** butonuna tıklayarak kaydedin.

**Önemli:** Statik route için çıkış arayüzü olarak SD-WAN Zone kullanıldığında ayrıca gateway tanımlamanız gerekmez.

FortiGate, SD-WAN Zone içerisindeki her üye arayüz için tanımlanan gateway bilgilerini otomatik olarak kullanır.

### Stop and Think! – Kontrol Sorusu

**Soru:** Statik route tablosunda iki farklı route bulunuyor:

- Oluşturduğunuz `underlay` SD-WAN Zone üzerinden varsayılan route.
- Önceden tanımlanmış `port1` üzerinden varsayılan route.

Bu yapılandırma geçerli midir?

**Cevap:**

Evet, bu yapılandırma geçerlidir.

FortiGate üzerinde SD-WAN Zone seviyesinde statik route tanımlanabildiği gibi, daha ayrıntılı yönlendirme kontrolü için SD-WAN üyesi olan arayüzler üzerinden de route tanımlanabilir.

## 5.2. DIA Trafiğine İzin Veren Firewall Policy Oluşturma

1. **Policy & Objects > Firewall Policy** menüsüne gidin.
2. **Create New** butonuna tıklayın.
3. Aşağıdaki ayarları yapılandırın.

### Genel Firewall Policy Ayarları

| Alan | Değer |
|---|---|
| Name | DIA |
| Incoming Interface | port3 |
| Outgoing Interface | underlay |
| Source | LOCAL_SUBNET |
| Destination | all |
| Schedule | always |
| Service | ALL |
| Action | Accept |
| NAT | Enable |

### Security Profiles Ayarları

**Application Control:**

- Application Control seçeneğini etkinleştirin.
- Profile olarak `default` seçin.

**SSL Inspection:**

Aşağıdaki profili seçin:

```text
certificate-inspection
```

### Logging Options Ayarları

- Log Allowed Traffic seçeneğini etkinleştirin.
- All Sessions seçeneğini işaretleyin.

4. **OK** butonuna tıklayarak firewall policy yapılandırmasını kaydedin.

Bu policy sayesinde Local-FortiGate üzerindeki `LOCAL_SUBNET` ağının internet trafiği `underlay` SD-WAN Zone üzerinden yönlendirilebilir.

---

# 6. Routing Table Kontrolü

Son aşamada Local-FortiGate üzerindeki aktif yönlendirme tablosu kontrol edilecektir.

### 6.1. SSH ile FortiGate'e Bağlanma

1. Local-FortiGate cihazına SSH bağlantısı açın.
2. `admin` kullanıcı adı ve `password` parolasıyla giriş yapın.
3. Aktif yönlendirme tablosunu görüntülemek için aşağıdaki komutu çalıştırın.

```bash
get router info routing-table all
```

### 6.2. Varsayılan Route Kontrolü

Yönlendirme tablosunda aşağıdaki iki arayüz üzerinden oluşturulan varsayılan rotaları kontrol edin:

- port1
- port2

Her iki varsayılan route'un da aynı distance değerine sahip olduğunu ve aktif yönlendirme tablosunda bulunduğunu doğrulayın.

**Önemli Bilgi:**

Bir SD-WAN Zone için statik route oluşturulduğunda FortiGate, zone içerisindeki tüm üye arayüzler için aynı distance değerine sahip ayrı rotalar oluşturur.

Böylece tüm uygun üye rotalar aktif yönlendirme tablosunda bulunur ve SD-WAN trafik yönlendirme mekanizması tarafından kullanılabilir.

---

# 7. Laboratuvar Kontrol Listesi

Aşağıdaki maddeleri kontrol ederek laboratuvar çalışmasının tamamlandığını doğrulayabilirsiniz.

### Interface ve SD-WAN Zone

- [ ] `Full_Access` firewall policy silindi.
- [ ] `underlay` isimli SD-WAN Zone oluşturuldu.
- [ ] `port1` SD-WAN üyesi olarak eklendi.
- [ ] `port2` SD-WAN üyesi olarak eklendi.
- [ ] port1 gateway: `10.200.1.254`
- [ ] port2 gateway: `10.200.2.254`

### Performance SLA

- [ ] `Level3_DNS` Performance SLA oluşturuldu.
- [ ] `4.2.2.1` sunucusu eklendi.
- [ ] `4.2.2.2` sunucusu eklendi.
- [ ] port1 ve port2 Participants olarak seçildi.
- [ ] Her iki arayüzün bağlantı durumu kontrol edildi.

### SD-WAN Rules

- [ ] `Critical-DIA` kuralı oluşturuldu.
- [ ] Slack-Slack Internet Service seçildi.
- [ ] Dropbox-Web Internet Service seçildi.
- [ ] Bloomberg Application seçildi.
- [ ] port1 ve port2 sıralı olarak tanımlandı.
- [ ] `Non-Critical-DIA` kuralı oluşturuldu.
- [ ] Addicting.Games uygulaması seçildi.
- [ ] Social.Media kategorisi seçildi.
- [ ] port2 tercih edilen arayüz olarak belirlendi.
- [ ] Implicit SD-WAN Rule için Source-Destination IP algoritması seçildi.

### Routing ve Firewall Policy

- [ ] `underlay` için varsayılan statik route oluşturuldu.
- [ ] DIA isimli firewall policy oluşturuldu.
- [ ] NAT etkinleştirildi.
- [ ] Application Control etkinleştirildi.
- [ ] SSL Inspection için certificate-inspection seçildi.
- [ ] Log Allowed Traffic etkinleştirildi.
- [ ] All Sessions seçildi.
- [ ] Routing table kontrol edildi.
- [ ] port1 ve port2 varsayılan rotalarının aktif olduğu doğrulandı.

---

# 8. Laboratuvar Sonucu

Bu laboratuvar çalışmasında FortiGate üzerinde temel bir SD-WAN ve Direct Internet Access (DIA) yapılandırması gerçekleştirilmiştir.

İlk olarak `port1` ve `port2` arayüzleri `underlay` isimli SD-WAN Zone içerisine eklenmiştir.

Ardından Performance SLA oluşturularak iki internet bağlantısının erişilebilirliği ve performansı izlenebilir hale getirilmiştir.

Kritik uygulamalar için `Critical-DIA`, kritik olmayan uygulamalar için `Non-Critical-DIA` isimli SD-WAN kuralları oluşturulmuştur.

Son olarak SD-WAN Zone için varsayılan statik route ve internet erişimini sağlayan DIA firewall policy yapılandırılmıştır.

Routing table kontrol edilerek her iki internet bağlantısının da aktif yönlendirme tablosunda bulunduğu doğrulanmıştır.

**Laboratuvar tamamlandı.**
