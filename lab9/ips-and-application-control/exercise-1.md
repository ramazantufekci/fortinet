# LAB-9 – Bilinen Exploit Saldırılarını Engelleme (Blocking Known Exploits)

Bu laboratuvar çalışmasında Local-FortiGate üzerinde IPS (Intrusion Prevention System – Saldırı Önleme Sistemi) incelemesini yapılandıracak ve izleyeceksiniz.

## 1. IPS İncelemesini Yapılandırma

Farklı önem seviyelerine sahip bilinen saldırıların imzalarını içeren bir IPS sensörü oluşturacaksınız.

### IPS incelemesini yapılandırmak için

1. Local-FortiGate GUI arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla oturum açın.
3. **Security Profiles > Intrusion Prevention** menüsüne tıklayın.
4. **Create New** seçeneğine tıklayın.
5. **Name** alanına `WEBSERVER` yazın.
6. **IPS Signatures and Filters** bölümünde **Create New** seçeneğine tıklayın.

### IPS filtrelerini ekleme

1. **Add Signatures** penceresinde filtre eklemek için **+** simgesine tıklayın.
2. Arama alanına `medium` yazın.
3. Orta önem seviyesindeki filtreyi seçmek için **SEV** seçeneğine tıklayın.
4. Arama alanındaki `medium` ifadesini silin ve `high` yazın.
5. Yüksek önem seviyesindeki filtreyi seçmek için **SEV** seçeneğine tıklayın.
6. Arama alanındaki `high` ifadesini silin ve `critical` yazın.
7. Kritik önem seviyesindeki filtreyi seçmek için **SEV** seçeneğine tıklayın.
8. Arama alanındaki `critical` ifadesini silin ve `Server` yazın.
9. Sunucu hedef filtresini seçmek için **TGT** seçeneğine tıklayın.
10. Seçilen filtreleri eklemek için **OK** seçeneğine tıklayın.
11. Arama alanındaki `Server` ifadesini silin ve `Apache` yazın.
12. Apache uygulama filtresini seçmek için **App** seçeneğine tıklayın.
13. Filtreleri eklemek için tekrar **OK** seçeneğine tıklayın.
14. IPS sensörü yapılandırmasını tamamlamak için **OK** seçeneğine tıklayın.

> **Önemli:** FortiGate, filtrelerle eşleşen tüm imzaları IPS sensörüne ekler. Bu nedenle filtreleri mümkün olduğunca spesifik yapılandırmanız gerekir.

Bu laboratuvarda FortiGate, bir Apache sunucusunu koruyacak ve ilgili imzalar için varsayılan aksiyonu uygulayacaktır.

---

## 2. IPS Sensörünü VIP Firewall Policy Üzerinde Uygulama

Oluşturduğunuz IPS sensörünü, Local-Client sanal makinesinde çalışan web sunucusuna dış ağdan erişim sağlayan güvenlik duvarı politikasına uygulayacaksınız.

### Uzmanlık Görevi (Expert Challenge)

Local-FortiGate GUI arayüzünde aşağıdaki işlemleri gerçekleştirin:

- Dış IP adresi `10.200.1.200` olan adresi, iç IP adresi `10.0.1.10` adresine yönlendiren yeni bir Virtual IP (VIP) oluşturun.
- Harici arayüz olarak `port1` kullanın.
- Virtual IP adını `VIP-WEB-SERVER` olarak belirleyin.
- VIP adresine gelen tüm trafiğe izin veren yeni bir Firewall Policy oluşturun.
- Firewall Policy üzerinde `WEBSERVER` IPS sensörünü etkinleştirin.
- Firewall Policy adını `Web_Server_Access_IPS` olarak belirleyin.

### 2.1. Virtual IP (VIP) Oluşturma

1. Local-FortiGate GUI üzerinde **Policy & Objects > Virtual IPs** menüsüne gidin.
2. **Create New** seçeneğine tıklayın.
3. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | VIP-WEB-SERVER |
| Interface | port1 |
| External IP address/range | 10.200.1.200 |
| Map to IPv4 address/range | 10.0.1.10 |

4. Yapılandırmayı kaydetmek için **OK** seçeneğine tıklayın.

### 2.2. Firewall Policy Oluşturma

1. **Policy & Objects > Firewall Policy** menüsüne gidin.
2. **Create New** seçeneğine tıklayın.
3. Aşağıdaki ayarları kullanarak yeni bir Firewall Policy oluşturun:

| Alan | Değer |
|---|---|
| Name | Web_Server_Access_IPS |
| Incoming Interface | port1 |
| Outgoing Interface | port3 |
| Source | all |
| Destination | VIP-WEB-SERVER |
| Schedule | always |
| Service | ALL |
| Action | ACCEPT |
| Inspection Mode | Flow-based |
| NAT | disabled |

### 2.3. Güvenlik Profillerini Etkinleştirme

Firewall Policy içerisindeki **Security Profiles** bölümünde:

1. **IPS** seçeneğini etkinleştirin.
2. IPS alanından `WEBSERVER` sensörünü seçin.
3. **SSL Inspection** alanında `certificate-inspection` seçeneğini belirleyin.
4. **OK** seçeneğine tıklayarak politikayı kaydedin.

> **Not:** Full SSL Inspection kullanılması laboratuvarın tamamlanma süresini önemli ölçüde artıracaktır. Bu nedenle bu çalışma kapsamında Full SSL Inspection yapılandırılmayacaktır.

---

## 3. Linux Sunucusundan Saldırı Trafiği Oluşturma

Bu aşamada Local-FortiGate'in önünde bulunan Linux sunucusundan Perl betiği çalıştırarak test saldırı trafiği oluşturacaksınız.

### Saldırı trafiği oluşturmak için

1. Local-Client sanal makinesinde PuTTY uygulamasını açın.
2. SSH üzerinden `LINUX` kayıtlı oturumuna bağlanın.
3. Aşağıdaki kullanıcı bilgileriyle giriş yapın:

| Alan | Değer |
|---|---|
| Username | student |
| Password | password |

4. Saldırı testini başlatmak için aşağıdaki komutu çalıştırın:

```bash
nikto.pl -host 10.200.1.200
```

5. PuTTY oturumunu açık bırakın. İsterseniz pencereyi küçültebilirsiniz.

> **Dikkat:** Trafik üretiminin devam etmesi için LINUX PuTTY oturumunu kapatmayın.

---

## 4. IPS Olaylarını İzleme (Monitor the IPS)

Bu aşamada Local-FortiGate tarafından algılanan ve engellenen bilinen saldırıları IPS logları üzerinden inceleyeceksiniz.

### IPS loglarını görüntülemek için

1. Local-FortiGate GUI arayüzüne geri dönün.
2. **Log & Report > Security Events > Intrusion Prevention** menüsüne gidin.
3. Algılanan ve engellenen saldırılara ait log kayıtlarını inceleyin.
4. İlgili bir log kaydına tıklayın.
5. **Details** seçeneğine tıklayın.
6. **Attack Name** bağlantısını açın.
7. İlgili imzayı FortiGuard Labs **Threat Encyclopedia** üzerinde inceleyin.

### FortiGuard Threat Encyclopedia

FortiGuard Labs Threat Encyclopedia, IPS imzaları hakkında aşağıdaki bilgileri sağlar:

- Önem seviyesi (Severity)
- Koruma kapsamı (Coverage)
- Etkilenen ürünler (Affected Products)
- Saldırının etkisi (Impact)
- Önerilen güvenlik önlemleri (Recommended Actions)

### Önemli değerlendirme

İncelediğiniz IPS imzalarının Local-Client sanal makinesinde kurulu olan ürünle eşleşip eşleşmediğini kontrol edin.

Eğer bir imza mevcut ürün veya uygulama için geçerli değilse, ilgili paketleri incelemek gerçekten gerekli midir?

Bu değerlendirme, `WEBSERVER` IPS sensörünü optimize ederken önemlidir. Gereksiz imzaların filtrelenmesi, güvenlik incelemesinin daha verimli yapılmasına yardımcı olabilir.

### IPS loglarının oluşturulduğu durumlar

FortiGate aşağıdaki durumlarda Intrusion Prevention log kaydı oluşturur:

- Saldırının algılanması ancak engellenmemesi.
- Saldırının engellenerek trafiğin düşürülmesi (Dropped).

---

## 5. IPS Etkinliğini Sorun Giderme (Troubleshoot IPS Activity)

Bu aşamada IPS motorunun durumunu kontrol edecek, bypass modunu etkinleştirecek ve IPS ile ilgili motorları yeniden başlatacaksınız.

### 5.1. IPS Monitor Durumunu Kontrol Etme

1. Local-FortiGate CLI arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla oturum açın.
3. IPS monitor durumunu görüntülemek için aşağıdaki komutu çalıştırın:

```bash
diagnose test application ipsmonitor 1
```

Bu komut IPS monitor işleminin mevcut durumunu görüntüler.

### 5.2. IPS Bypass Modunu Etkinleştirme

IPS bypass modunu etkinleştirmek için aşağıdaki komutu çalıştırın:

```bash
diagnose test application ipsmonitor 5
```

Ardından durumu tekrar kontrol edin:

```bash
diagnose test application ipsmonitor 1
```

Çıktının son satırında bypass durumunun `enable` olduğunu göreceksiniz.

### Bypass modunu doğrulama

Local-FortiGate GUI arayüzünde:

**Log & Report > Security Events > Intrusion Prevention**

menüsüne gidin.

Bypass modu etkinleştirildikten sonra yeni IPS log kayıtlarının oluşturulmadığını doğrulayın.

### 5.3. IPS Motorlarını Yeniden Başlatma

IPS ile ilgili motorları yeniden başlatmak için aşağıdaki komutu çalıştırın:

```bash
diagnose test application ipsmonitor 99
```

İşlemin tamamlanmasının ardından IPS durumunu tekrar kontrol edin:

```bash
diagnose test application ipsmonitor 1
```

### Yeniden başlatma sonrası beklenen durum

IPS motorunu yeniden başlattığınızda:

- İlgili IPS işleminin Process ID (PID) değeri değişir.
- Bypass modu varsayılan durumuna geri döner.
- Bypass durumu `disable` olarak görüntülenir.

---

## 6. Laboratuvar Çalışmasını Tamamlama

Aşağıdaki işlemleri gerçekleştirerek laboratuvarı tamamlayın:

1. Local-FortiGate CLI oturumunu kapatın.
2. Local-FortiGate GUI oturumunu kapatın.
3. LINUX PuTTY oturumunu kapatın.

---

## 7. Kullanılan Komutlar – Hızlı Referans

| Komut | Açıklama |
|---|---|
| `diagnose test application ipsmonitor 1` | IPS monitor durumunu görüntüler. |
| `diagnose test application ipsmonitor 5` | IPS bypass modunu etkinleştirir. |
| `diagnose test application ipsmonitor 99` | IPS ile ilgili motorları yeniden başlatır. |
| `nikto.pl -host 10.200.1.200` | Web sunucusuna yönelik güvenlik testi trafiği oluşturur. |

---

## 8. Laboratuvar Sonu Kontrol Listesi

- [ ] WEBSERVER isimli IPS sensörü oluşturuldu.
- [ ] Medium severity filtresi eklendi.
- [ ] High severity filtresi eklendi.
- [ ] Critical severity filtresi eklendi.
- [ ] Server target filtresi eklendi.
- [ ] Apache application filtresi eklendi.
- [ ] VIP-WEB-SERVER oluşturuldu.
- [ ] Dış IP adresi `10.200.1.200` olarak ayarlandı.
- [ ] İç IP adresi `10.0.1.10` olarak ayarlandı.
- [ ] Web_Server_Access_IPS Firewall Policy oluşturuldu.
- [ ] WEBSERVER IPS sensörü Firewall Policy üzerine uygulandı.
- [ ] SSL Inspection `certificate-inspection` olarak ayarlandı.
- [ ] Nikto test trafiği oluşturuldu.
- [ ] IPS log kayıtları incelendi.
- [ ] IPS bypass modu test edildi.
- [ ] IPS motorları yeniden başlatıldı.
- [ ] Bypass durumunun `disable` olduğu doğrulandı.

**LAB-9 tamamlandı.**
