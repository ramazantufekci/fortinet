# LAB-1: FortiGate Sistem Ayarlarının Yapılandırılması

## 1. Amaç

Bu laboratuvar çalışmasında Local-FortiGate cihazının sistem ayarları incelenecek ve ağ üzerinde çalışabilmesi için gerekli yapılandırmalar yapılacaktır.

Bu çalışma kapsamında, ağa bağlanan cihazların otomatik olarak IP adresi alabilmesi için FortiGate üzerindeki dahili ağ DHCP sunucusu etkinleştirilecektir.

**Not:** Bu laboratuvarda bazı ayarlar önceden yapılandırılmıştır. Dolayısıyla mevcut ayarlar FortiGate'in fabrika varsayılan ayarlarından farklı olabilir.

---

## 2. Local-FortiGate Ağ Ayarlarının İncelenmesi

Bu bölümde Local-FortiGate cihazının `port3` ağ arayüzü ve statik yönlendirme ayarları incelenecektir.

### 2.1. port3 Ağ Arayüzünün İncelenmesi

1. Local-FortiGate GUI arayüzüne bağlanın.
2. Aşağıdaki kullanıcı bilgileriyle oturum açın:
   - **Kullanıcı adı:** `admin`
   - **Şifre:** `password`
3. Sol menüden **Network > Interfaces** bölümüne gidin.
4. Listeden **port3** arayüzünü seçin.
5. **Edit** butonuna tıklayın.

#### Arayüz Bilgilerinin İncelenmesi

Açılan **Edit Interface** penceresinin sağ tarafında bulunan bilgileri inceleyin.

FortiGate, mevcut çalışma ekranından ayrılmanıza gerek kalmadan cihazın ana bilgisayar adını (Hostname) ve durumunu (Status) görüntülemenizi sağlar.

#### Arayüz Rolünün Değiştirilmesi

1. **Role** alanını bulun.
2. Bu alandan **WAN** seçeneğini seçin.

**Düşün ve Değerlendir:**

Arayüzün rolü değiştirildiğinde bazı ayarların görünmesinin veya kaybolmasının sebebi nedir?

**Cevap:**

Her arayüz rolü, ilgili ağ bağlantısının amacına uygun yapılandırma seçeneklerini gösterir.

- **WAN:** Dış ağa veya internete bağlantı için kullanılan arayüzdür.
- **LAN:** Yerel ağ bağlantısı için kullanılan arayüzdür.
- **Undefined:** Arayüz üzerinde yapılandırılabilecek tüm ayarların görüntülenmesini sağlar.

WAN rolünün seçilmesindeki amaç, arayüz harici bir ağa bağlandığında bazı ayarların devre dışı bırakılması gerekebileceğini ve DHCP sunucusu yapılandırma seçeneklerinin değişebileceğini görmektir.

#### Tahmini Bant Genişliğinin İncelenmesi

1. Arayüz rolü WAN olarak ayarlandığında **Estimated bandwidth** alanlarını inceleyin.
2. Aşağıdaki değerleri gözden geçirin:
   - **Downstream:** İndirme yönündeki maksimum bant genişliği.
   - **Upstream:** Yükleme yönündeki maksimum bant genişliği.

WAN rolü seçildiğinde, arayüzün maksimum indirme ve yükleme bant genişliği değerleri belirlenebilir.

3. Herhangi bir değişikliği kaydetmeden çıkmak için **Cancel** butonuna tıklayın.

---

### 2.2. Local-FortiGate Statik Varsayılan Ağ Geçidinin İncelenmesi

Bu bölümde FortiGate üzerinde tanımlanmış statik varsayılan ağ geçidi incelenecektir.

1. FortiGate GUI üzerinde **Network > Static Routes** bölümüne gidin.
2. Listelenen statik rota kaydını seçin.
3. **Edit** butonuna tıklayın.
4. **Advanced Options** bölümünü genişletin.

#### Statik Rota Önceliği (Priority)

Statik rotalarda Priority değeri, yönlendirme kararlarında kullanılan önemli bir parametredir.

İki statik rotanın Distance değerleri eşit olduğunda, Priority değeri daha düşük olan rota tercih edilir.

**Örnek:**

| Rota | Distance | Priority |
|---|---:|---:|
| Rota 1 | 10 | 5 |
| Rota 2 | 10 | 10 |

Bu örnekte Distance değerleri eşit olduğu için Priority değeri 5 olan Rota 1 tercih edilir.

5. İnceleme tamamlandıktan sonra **Cancel** butonuna tıklayın.

---

## 3. Local-FortiGate Üzerinde DHCP Sunucusunun Etkinleştirilmesi

Bu bölümde `port3` arayüzü üzerinde DHCP sunucusu etkinleştirilecektir.

DHCP (Dynamic Host Configuration Protocol), ağa bağlanan cihazlara otomatik olarak IP adresi ve diğer ağ yapılandırma bilgilerini dağıtan protokoldür.

### 3.1. port3 Üzerinde DHCP Sunucusunun Etkinleştirilmesi

1. FortiGate GUI üzerinde **Network > Interfaces** bölümüne gidin.
2. Listeden **port3** arayüzünü seçin.
3. **Edit** butonuna tıklayın.
4. **Role** alanından **LAN** seçeneğini seçin.

5. **DHCP Server** seçeneğini etkinleştirin.

**Önemli Not:**

Arayüz rolü LAN olarak seçildiğinde DHCP Server yapılandırma seçeneği GUI üzerinde görünür.

WAN rolünde bu seçenek aynı şekilde sunulmaz. Bunun nedeni, arayüz rollerinin kullanım amaçlarına göre farklı yapılandırma seçenekleri sağlamasıdır.

### 3.2. DHCP IP Adres Aralığının Tanımlanması

1. DHCP Server seçeneğinin etkin olduğundan emin olun.
2. **Address Range** alanına aşağıdaki IP adres aralığını girin:

```text
10.0.1.1-10.0.1.250
```

Bu adres aralığı, DHCP sunucusunun istemcilere dağıtabileceği IP adreslerini belirtir.

| Parametre | Değer |
|---|---|
| Arayüz | port3 |
| Arayüz Rolü | LAN |
| DHCP Server | Enabled |
| Başlangıç IP | 10.0.1.1 |
| Bitiş IP | 10.0.1.250 |

3. Yapılandırmayı kaydetmek için **OK** butonuna tıklayın.

---

## 4. Laboratuvar Çalışmasının Özeti

Bu laboratuvar çalışmasında aşağıdaki işlemler gerçekleştirilmiştir:

- [x] Local-FortiGate GUI arayüzüne giriş yapıldı.
- [x] port3 ağ arayüzü incelendi.
- [x] Arayüz rolleri (WAN, LAN ve Undefined) incelendi.
- [x] WAN rolündeki bant genişliği ayarları gözden geçirildi.
- [x] Statik varsayılan ağ geçidi incelendi.
- [x] Statik rotaların Distance ve Priority değerleri öğrenildi.
- [x] port3 arayüzünün rolü LAN olarak değiştirildi.
- [x] DHCP Server etkinleştirildi.
- [x] DHCP IP adres aralığı `10.0.1.1-10.0.1.250` olarak tanımlandı.
- [x] Yapılandırma kaydedildi.

## 5. Kontrol Edilmesi Gerekenler

Laboratuvar tamamlandığında aşağıdaki ayarların doğru olduğundan emin olun:

| Kontrol | Beklenen Değer |
|---|---|
| Arayüz | port3 |
| Role | LAN |
| DHCP Server | Enabled |
| DHCP Address Range | 10.0.1.1 - 10.0.1.250 |

**LAB-1 tamamlandı.**

Local-FortiGate üzerinde port3 arayüzü LAN olarak yapılandırılmış ve DHCP sunucusu etkinleştirilmiştir.
