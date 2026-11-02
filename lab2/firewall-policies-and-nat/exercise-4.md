# LAB-2: IP Pool Kullanarak Dynamic NAT Yapılandırması

IP Pool (IP Adres Havuzu), kaynak IP adresini çıkış arayüzünün IP adresi yerine belirlenen IP havuzundan bir adresle değiştirmek için kullanılır.

Mevcut yapılandırmada Local-FortiGate, Local-Client VM tarafından oluşturulan tüm trafiğin kaynak IP adresini `10.200.1.200` olarak değiştirmektedir.

Bunun nedeni, VIP nesnesinin dahili IP adresinin Local-Client makinesinin IP adresiyle eşleşmesi ve VIP'nin Static NAT VIP olarak yapılandırılmış olmasıdır.

Bu laboratuvarda aşağıdaki işlemleri gerçekleştireceksiniz:

- Yeni bir IP Pool oluşturacaksınız.
- Oluşturduğunuz IP Pool'u ingress-to-egress (içeriden dışarıya) firewall policy üzerinde kullanacaksınız.
- CLI komutlarıyla SNAT adresini kontrol edeceksiniz.
- IP Pool kullanıldığında Static NAT VIP davranışının nasıl değiştiğini inceleyeceksiniz.

---

## 1. IP Pool Oluşturma

Bu adımda, dışarıya çıkış yapılan `port1` arayüzünün subnet'i içerisinde bulunan bir IP adresini kullanarak IP Pool oluşturacaksınız.

### IP Pool oluşturmak için

1. **Local-FortiGate** GUI arayüzüne bağlanın.
2. Kullanıcı adı `admin` ve parola `password` bilgileriyle oturum açın.
3. **Policy & Objects > IP Pools** menüsüne gidin.
4. **Create New** butonuna tıklayın.
5. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `INTERNAL-HOST-EXT-IP` |
| External IP Range | `10.200.1.100-10.200.1.100` |

6. **OK** butonuna tıklayarak IP Pool nesnesini kaydedin.

**Açıklama:**

Oluşturduğunuz IP Pool yalnızca tek bir IP adresinden oluşmaktadır:

`10.200.1.100`

Bu adres, SNAT işlemi sırasında kaynak IP adresi olarak kullanılacaktır.

---

## 2. Firewall Policy'yi IP Pool Kullanacak Şekilde Düzenleme

Şimdi oluşturduğunuz IP Pool'u ingress-to-egress yönündeki firewall policy üzerine uygulayacaksınız.

Bu işlem sayesinde mevcut Static NAT davranışı yerine Dynamic NAT kullanılmasını sağlayacaksınız.

### Firewall Policy düzenlemek için

1. **Local-FortiGate** GUI arayüzünde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. `Full_Access` isimli firewall policy üzerine sağ tıklayın.
3. **Set Status > Enable** seçeneğine tıklayarak policy'yi etkinleştirin.
4. Aynı firewall policy üzerine tekrar sağ tıklayın.
5. **Edit** seçeneğine tıklayın.
6. **Firewall/Network Options** bölümüne gidin.
7. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| NAT | Etkin (`Enable`) |
| IP Pool Configuration | `Use Dynamic IP Pool` |

8. **Use Dynamic IP Pool** seçeneğini etkinleştirdiğinizde görünen `+` simgesine tıklayın.
9. Sağ tarafta açılan bölümden `INTERNAL-HOST-EXT-IP` IP Pool nesnesini seçin.

Yapılandırmanız aşağıdaki değerleri içermelidir:

- NAT: Enable
- IP Pool Configuration: Use Dynamic IP Pool
- IP Pool: INTERNAL-HOST-EXT-IP

10. **OK** butonuna tıklayarak firewall policy değişikliklerini kaydedin.

**Önemli:** Artık `Full_Access` policy üzerinden çıkan ve ilgili IP Pool yapılandırmasıyla eşleşen trafik için SNAT adresi, IP Pool içerisindeki `10.200.1.100` adresi olacaktır.

---

## 3. IP Pool ile Dynamic NAT İşlemini Test Etme

Yapılandırmayı tamamladınız. Şimdi Local-Client VM üzerinden internetteki farklı web sitelerine bağlanarak Dynamic NAT işlemini test edeceksiniz.

İşlem başarılı olduğunda Local-Client VM'nin `10.0.1.10` IP adresinin, IP Pool içerisindeki `10.200.1.100` adresine çevrildiğini göreceksiniz.

### 3.1. Mevcut Oturumları Temizleme

1. **Local-FortiGate** CLI arayüzüne geçin.
2. Kullanıcı adı `admin` ve parola `password` bilgileriyle oturum açın.
3. Aşağıdaki komutları sırasıyla çalıştırın:

```bash
diagnose sys session filter clear
diagnose sys session filter src 10.0.1.10
diagnose sys session clear
```

**Komutların açıklaması:**

| Komut | Açıklama |
|---|---|
| `diagnose sys session filter clear` | Önceden tanımlanmış oturum filtrelerini temizler. |
| `diagnose sys session filter src 10.0.1.10` | Kaynak IP adresi `10.0.1.10` olan oturumları filtreler. |
| `diagnose sys session clear` | Tanımlanan filtreye uyan oturumları temizler. |

**Önemli Bilgi:**

Bu işlemde önce `10.0.1.10` kaynak IP adresine sahip oturumlar için filtre oluşturulur.

Ardından `diagnose sys session clear` komutu çalıştırıldığında yalnızca bu filtreyle eşleşen oturumlar temizlenir.

Böylece SSH oturumunuz gibi diğer bağlantılar kesilmez.

**Dikkat:** Session Clear komutundan önce filtre oluşturmak önemlidir. Filtre kullanılmadan oturumları temizlemek, yönetim bağlantınızın da kesilmesine neden olabilir.

### 3.2. Local-Client Üzerinden İnternet Trafiği Oluşturma

1. **Local-Client** sanal makinesine geçin.
2. Bir web tarayıcısı açın.
3. Birkaç farklı tarayıcı sekmesi açarak aşağıdaki web sitelerine bağlanın:

   - [www.fortinet.com](https://www.fortinet.com/)
   - [www.yahoo.com](https://www.yahoo.com/)
   - [www.bbc.com](https://www.bbc.com/)

Bu bağlantılar sayesinde Local-Client üzerinden dış ağa doğru yeni oturumlar oluşturulacaktır.

### 3.3. SNAT Adresini CLI Üzerinden Doğrulama

1. **Local-FortiGate** CLI oturumuna geri dönün.
2. Aşağıdaki komutu çalıştırın:

```bash
get system session list
```

Bu komut, FortiGate üzerindeki aktif oturumları ve bu oturumlarda uygulanan NAT işlemlerini görüntüler.

Komut çıktısında SNAT adresini kontrol edin.

<badge color="success">Beklenen sonuç</badge>

SNAT adresi artık `10.200.1.100` olmalıdır.

**Açıklama:**

Önceki laboratuvarda Static NAT VIP nedeniyle SNAT adresi olarak `10.200.1.200` kullanılıyordu.

Bu laboratuvarda ise `Full_Access` firewall policy üzerinde Dynamic IP Pool yapılandırdığınız için SNAT adresi `10.200.1.100` olarak değiştirilmiştir.

Böylece IP Pool yapılandırması, Static NAT VIP tarafından kullanılan SNAT adresinin önüne geçmiştir.

| Özellik | Önceki yapılandırma | Yeni yapılandırma |
|---|---|---|
| NAT yöntemi | Static NAT VIP | Dynamic NAT / IP Pool |
| SNAT adresi | `10.200.1.200` | `10.200.1.100` |
| Kaynak IP | `10.0.1.10` | `10.0.1.10` |

4. Local-FortiGate CLI penceresini kapatın.
5. Local-FortiGate GUI sekmesi dışındaki tüm tarayıcı sekmelerini kapatın.

---

## 4. Laboratuvar Sonucu

Bu laboratuvar kapsamında aşağıdaki işlemleri gerçekleştirdiniz:

- `INTERNAL-HOST-EXT-IP` isimli IP Pool oluşturdunuz.
- IP Pool içerisine `10.200.1.100` adresini tanımladınız.
- `Full_Access` firewall policy kuralını etkinleştirdiniz.
- Firewall policy üzerinde NAT seçeneğini etkinleştirdiniz.
- IP Pool Configuration ayarını `Use Dynamic IP Pool` olarak değiştirdiniz.
- Oluşturduğunuz IP Pool'u firewall policy üzerine uyguladınız.
- `10.0.1.10` kaynak adresine sahip mevcut oturumları filtreleyerek temizlediniz.
- Local-Client üzerinden internet trafiği oluşturdunuz.
- `get system session list` komutuyla SNAT adresini doğruladınız.
- SNAT adresinin `10.200.1.200` yerine `10.200.1.100` olduğunu gözlemlediniz.

### Öğrenilmesi Gereken Temel Kavram

**IP Pool**, SNAT işlemi sırasında kaynak IP adresinin belirli bir IP adresi havuzundan seçilmesini sağlar.

- **Static NAT VIP:** Uygun koşullarda VIP'nin External IP adresini SNAT adresi olarak kullanabilir.
- **Dynamic NAT with IP Pool:** Firewall policy üzerinde tanımlanan IP Pool içerisindeki adresi SNAT adresi olarak kullanır.

**LAB-2 tamamlandı.**
