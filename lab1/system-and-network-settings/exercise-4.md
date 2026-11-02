# LAB-1: FortiGate Yönetici Hesaplarının Yapılandırılması

## 1. Amaç

FortiGate, yönetici hesaplarının yetkilerini yapılandırmak için birçok seçenek sunar.

Örneğin, yöneticilerin FortiGate cihazına yalnızca belirli IP adreslerinden veya güvenilir ağlardan bağlanmasına izin verilebilir.

Bu laboratuvar çalışmasında yönetici profilleri (Administrator Profiles) ve yönetici kullanıcı hesapları (Administrator Accounts) oluşturulacak ve yapılandırılacaktır.

**Administrator Profile (Yönetici Profili):**

Yönetici profili, bir yönetici kullanıcısına atanabilen ve kullanıcının FortiGate GUI ve CLI üzerinde hangi işlemleri gerçekleştirebileceğini belirleyen yetki grubudur.

Bu çalışma kapsamında:

- Yeni bir yönetici profili oluşturulacaktır.
- Yönetici hesabı oluşturulacaktır.
- Yönetici hesabına özel yetkiler atanacaktır.
- Yönetici hesabının erişim yetkileri test edilecektir.
- Trusted Hosts kullanılarak yönetici erişimi sınırlandırılacaktır.
- CLI üzerinden ikinci bir güvenilir IP ağı tanımlanacaktır.

---

## 2. Yönetici Kullanıcı Profilinin Oluşturulması

Bu bölümde, yapılandırma ayarlarının büyük bölümünde yalnızca okuma yetkisine sahip olacak yeni bir yönetici profili oluşturulacaktır.

### 2.1. FortiGate GUI Arayüzüne Giriş

1. Local-FortiGate GUI arayüzüne bağlanın.
2. Aşağıdaki bilgilerle oturum açın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `admin` |
| Şifre | `password` |

3. Sol menüden **System > Admin Profiles** bölümüne gidin.
4. **Create New** butonuna tıklayın.

### 2.2. Yeni Yönetici Profilinin Yapılandırılması

Açılan yönetici profili oluşturma ekranında aşağıdaki ayarları yapın:

| Alan | Değer |
|---|---|
| Name | `Security_Admin_Profile` |
| Security Profile | Read/Write |
| Diğer tüm izinler | Read |

**Yetki Açıklamaları:**

- **Read:** Kullanıcı ilgili yapılandırmayı görüntüleyebilir ancak değiştiremez.
- **Read/Write:** Kullanıcı ilgili yapılandırmayı görüntüleyebilir ve değiştirebilir.

Bu yapılandırmayla yönetici, yalnızca Security Profile ayarlarını değiştirebilir. Diğer yapılandırma alanlarında sadece görüntüleme yetkisine sahip olur.

5. Ayarları kaydetmek için **OK** butonuna tıklayın.

---

## 3. Yeni Yönetici Hesabının Oluşturulması

Bu bölümde yeni bir yönetici hesabı oluşturulacak ve önceki aşamada oluşturulan `Security_Admin_Profile` profili bu hesaba atanacaktır.

Oluşturulacak yönetici hesabı, yapılandırma ayarlarının büyük bölümünde salt okunur (Read-Only) yetkiye sahip olacaktır.

### 3.1. Yönetici Hesabı Oluşturma

1. FortiGate GUI üzerinde **System > Administrators** bölümüne gidin.
2. **Create New** butonuna tıklayın.
3. Açılan menüden **Administrator** seçeneğini seçin.

### 3.2. Yönetici Hesabı Bilgilerinin Girilmesi

Açılan **New Administrator** ekranında aşağıdaki bilgileri girin:

| Alan | Değer |
|---|---|
| Username | `Security` |
| Type | Local User |
| Password | `fortinet` |
| Confirm Password | `fortinet` |
| Administrator Profile | `Security_Admin_Profile` |

**Önemli Not:**

- Yönetici kullanıcı adları büyük/küçük harfe duyarlıdır (Case Sensitive).
- Yönetici parolaları büyük/küçük harfe duyarlıdır.
- Kullanıcı adı içerisinde aşağıdaki karakterler kullanılamaz:

```text
< > ( ) # "
```

4. Yapılandırmayı kaydetmek için **OK** butonuna tıklayın.

Böylece `Security` isimli yönetici hesabı oluşturulmuş olur.

---

## 4. Yeni Yönetici Hesabının Test Edilmesi

Bu bölümde oluşturulan `Security` hesabının yalnızca Security Profile yapılandırmalarında Read/Write yetkisine sahip olduğu doğrulanacaktır.

### 4.1. Admin Hesabından Çıkış Yapılması

1. Local-FortiGate GUI arayüzünde sağ üst köşedeki **admin** menüsüne tıklayın.
2. **Logout** seçeneğini kullanarak mevcut oturumu kapatın.

### 4.2. Security Hesabıyla Giriş Yapılması

1. Tekrar Local-FortiGate GUI giriş ekranına gidin.
2. Aşağıdaki bilgilerle oturum açın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `Security` |
| Şifre | `fortinet` |

3. İlk girişte **FortiGate Setup** penceresi açılırsa **Later** seçeneğine tıklayın.
4. FortiOS tanıtım penceresinde **Don't show again** seçeneğini etkinleştirin.
5. Pencereyi kapatmak için **OK** butonuna tıklayın.

### 4.3. Kullanıcı Yetkilerinin Kontrol Edilmesi

FortiGate GUI üzerinde görüntülenebilen menüleri ve yapılandırma seçeneklerini inceleyin.

Beklenen sonuç:

- Kullanıcı, çoğu yapılandırmayı yalnızca görüntüleyebilir.
- Security Profile ayarlarını görüntüleyebilir ve değiştirebilir.
- Diğer yetki alanlarında değişiklik yapamaz.

Bu işlemle `Security_Admin_Profile` profilinin doğru şekilde çalıştığı doğrulanır.

6. Test tamamlandıktan sonra GUI üzerinden oturumu kapatın.

---

## 5. Yönetici Erişiminin Sınırlandırılması (Trusted Hosts)

Bu bölümde FortiGate yöneticilerinin erişimi güvenilir IP adresleriyle sınırlandırılacaktır.

**Trusted Hosts**, bir yönetici hesabının FortiGate cihazına hangi IP adreslerinden veya IP ağlarından bağlanabileceğini belirleyen güvenlik özelliğidir.

Bu özellik sayesinde yalnızca belirlenen güvenilir ağlardan gelen yönetici bağlantılarına izin verilir.

### 5.1. Trusted Host Yapılandırmasının Yapılması

1. Local-Client VM üzerinde tarayıcıyı açın.
2. Local-FortiGate GUI arayüzüne bağlanın.
3. `admin` hesabıyla oturum açın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `admin` |
| Şifre | `password` |

4. **System > Administrators** bölümüne gidin.
5. Listeden **Security** hesabını bulun.
6. Hesabı düzenlemek için **Edit** seçeneğine tıklayın.

### 5.2. Güvenilir IP Ağının Tanımlanması

1. **Restrict login to trusted hosts** seçeneğini etkinleştirin.
2. **Trusted Host 1** alanına aşağıdaki IP ağını girin:

```text
10.200.3.0/24
```

Bu ağın anlamı:

| Parametre | Değer |
|---|---|
| Network Address | `10.200.3.0` |
| Subnet Mask | `255.255.255.0` |
| CIDR | `/24` |
| IP Aralığı | `10.200.3.1 - 10.200.3.254` |

3. Yapılandırmayı kaydetmek için **OK** butonuna tıklayın.
4. GUI oturumundan çıkış yapın.

**Sonuç:**

Artık `Security` hesabı yalnızca `10.200.3.0/24` ağı üzerinden FortiGate cihazına bağlanabilir.

Bu ağın dışında kalan IP adreslerinden gelen bağlantılar engellenir.

---

## 6. Kısıtlanmış Yönetici Erişiminin Test Edilmesi

Bu bölümde Trusted Hosts yapılandırmasının doğru çalışıp çalışmadığı test edilecektir.

### 6.1. Local-Client Üzerinden Erişimin Test Edilmesi

1. Local-Client VM üzerinde Local-FortiGate GUI oturumunu kapatın.
2. Tekrar giriş ekranına gidin.
3. `Security` hesabıyla giriş yapmayı deneyin:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `Security` |
| Şifre | `fortinet` |

**Beklenen Sonuç:**

Kimlik doğrulama başarısız olacaktır.

Bunun nedeni Local-Client VM'nin IP adresinin `10.200.3.0/24` güvenilir ağı içerisinde bulunmamasıdır.

### 6.2. Remote-Client Üzerinden Erişimin Test Edilmesi

1. Remote-Client VM üzerine geçin.
2. Aşağıdaki bilgilerle oturum açın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `Administrator` |
| Şifre | `password` |

3. Remote-Client üzerinde web tarayıcısını açın.
4. Adres çubuğuna aşağıdaki IP adresini girin:

```text
10.200.1.1
```

5. Local-FortiGate GUI giriş ekranında aşağıdaki bilgilerle oturum açın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `Security` |
| Şifre | `fortinet` |

**Beklenen Sonuç:**

Giriş başarılı olacaktır.

Çünkü Remote-Client'ın bağlantı IP adresi `10.200.3.0/24` güvenilir ağı içerisindedir.

### 6.3. Düşün ve Değerlendir

**Soru:**

Local-Client üzerinden doğrudan Local-FortiGate GUI bağlantısı kurulduğunda neden `admin` hesabıyla giriş yapılabilirken `Security` hesabıyla giriş yapılamadı?

**Cevap:**

Bunun nedeni Trusted Host yapılandırmasının yalnızca `Security` yönetici hesabına uygulanmış olmasıdır.

- `Security` hesabında Trusted Host kısıtlaması bulunmaktadır.
- `admin` hesabında bu kısıtlama bulunmamaktadır.

Dolayısıyla `admin` hesabı, ilgili başka erişim kontrolleri de izin verdiği sürece farklı IP adreslerinden bağlantı kurabilirken `Security` hesabı yalnızca tanımlanan güvenilir ağlardan bağlanabilir.

---

## 7. CLI Üzerinden İkinci Trusted Host Ağının Eklenmesi

Bu bölümde `Security` hesabına ikinci bir güvenilir IP ağı CLI üzerinden tanımlanacaktır.

Mevcut Trusted Host 1 ayarına ek olarak Trusted Host 2 yapılandırılacaktır.

### 7.1. FortiGate CLI Üzerinde Oturum Açma

1. Local-FortiGate CLI arayüzünü açın.
2. Aşağıdaki bilgilerle giriş yapın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `admin` |
| Şifre | `password` |

### 7.2. Trusted Host 2 Yapılandırmasının Eklenmesi

Aşağıdaki CLI komutlarını sırasıyla girin:

**1. Sistem yönetici yapılandırmasına geçin:**

```bash
config system admin
```

**2. Security yönetici hesabını seçin:**

```bash
edit Security
```

**3. İkinci güvenilir IP ağını tanımlayın:**

```bash
set trusthost2 10.0.1.0/24
```

**4. Yapılandırmadan çıkın ve değişiklikleri uygulayın:**

```bash
end
```

### 7.3. CLI Komutlarının Açıklaması

| Komut | Açıklama |
|---|---|
| `config system admin` | Yönetici hesaplarının yapılandırma bölümüne girer. |
| `edit Security` | Security isimli yönetici hesabını düzenler. |
| `set trusthost2 10.0.1.0/24` | İkinci güvenilir IP ağını tanımlar. |
| `end` | Yapılandırma modundan çıkar ve değişiklikleri uygular. |

### 7.4. Trusted Host Yapılandırmasının Son Hali

İşlem tamamlandığında `Security` hesabı için iki güvenilir ağ tanımlanmış olur:

| Trusted Host | IP Ağı | Açıklama |
|---|---|---|
| Trusted Host 1 | `10.200.3.0/24` | Birinci güvenilir ağ |
| Trusted Host 2 | `10.0.1.0/24` | İkinci güvenilir ağ |

Artık `Security` hesabı bu iki ağ üzerinden FortiGate cihazına erişebilir.

---

## 8. Son Erişim Testinin Yapılması

İkinci Trusted Host ağı eklendikten sonra Local-Client üzerinden tekrar bağlantı testi yapılacaktır.

1. Local-Client VM üzerine geri dönün.
2. Web tarayıcısını açın.
3. Local-FortiGate GUI adresine gidin:

```text
10.0.1.254
```

4. Aşağıdaki bilgilerle giriş yapın:

| Alan | Değer |
|---|---|
| Kullanıcı adı | `Security` |
| Şifre | `fortinet` |

**Beklenen Sonuç:**

Giriş başarılı olacaktır.

Çünkü Local-Client üzerinden yapılan bağlantının kaynak IP adresi artık ikinci Trusted Host olarak tanımlanan `10.0.1.0/24` ağı içerisindedir.

Böylece Trusted Host yapılandırmasının hem GUI hem de CLI üzerinden doğru şekilde çalıştığı doğrulanmış olur.

---

## 9. Laboratuvar Çalışmasının Özeti

Bu laboratuvar çalışmasında aşağıdaki işlemler gerçekleştirilmiştir:

- [x] FortiGate GUI arayüzüne admin hesabıyla giriş yapıldı.
- [x] `Security_Admin_Profile` isimli yönetici profili oluşturuldu.
- [x] Security Profile yetkisi Read/Write olarak ayarlandı.
- [x] Diğer tüm yönetici izinleri Read olarak yapılandırıldı.
- [x] `Security` isimli yeni yönetici hesabı oluşturuldu.
- [x] Yönetici hesabına `Security_Admin_Profile` profili atandı.
- [x] Yeni yönetici hesabıyla oturum açıldı.
- [x] Yönetici hesabının yetkileri test edildi.
- [x] Trusted Host kısıtlaması etkinleştirildi.
- [x] Trusted Host 1 olarak `10.200.3.0/24` ağı tanımlandı.
- [x] Local-Client üzerinden erişim testi yapıldı.
- [x] Remote-Client üzerinden erişim testi yapıldı.
- [x] CLI üzerinden Trusted Host 2 yapılandırıldı.
- [x] İkinci güvenilir ağ olarak `10.0.1.0/24` tanımlandı.
- [x] Local-Client üzerinden son erişim testi başarıyla gerçekleştirildi.

---

## 10. Önemli Kavramlar

| Kavram | Açıklama |
|---|---|
| Administrator | FortiGate yönetici hesabı. |
| Administrator Profile | Yönetici hesabının yetkilerini belirleyen profil. |
| Read | Yalnızca görüntüleme yetkisi. |
| Read/Write | Görüntüleme ve değiştirme yetkisi. |
| Local User | FortiGate üzerinde yerel olarak oluşturulan kullanıcı. |
| Trusted Hosts | Yönetici erişimine izin verilen güvenilir IP adresleri veya ağlar. |
| Trusted Host 1 | Birinci güvenilir IP ağı. |
| Trusted Host 2 | İkinci güvenilir IP ağı. |
| CIDR | IP ağlarının adres ve maske uzunluğuyla gösterilme yöntemi. |
| CLI | Komut satırı arayüzü. |
| GUI | Grafiksel kullanıcı arayüzü. |

---

## 11. Önemli Güvenlik Tavsiyeleri

1. Her yöneticiye yalnızca görevini yerine getirebilmesi için gereken yetkileri verin.
2. Günlük işlemler için tam yetkili `admin` hesabı yerine sınırlı yetkili hesaplar kullanın.
3. Yönetici hesaplarında Trusted Hosts özelliğini etkinleştirin.
4. Güvenilir IP ağlarını mümkün olduğunca dar tutun.
5. Yönetici parolalarını güçlü ve benzersiz belirleyin.
6. Kullanılmayan yönetici hesaplarını devre dışı bırakın veya silin.
7. Yönetici erişimlerini düzenli olarak kontrol edin.

---

**LAB-1 – Configuring Administrator Accounts tamamlandı.**

Bu laboratuvar sayesinde FortiGate üzerinde yönetici profilleri oluşturmayı, kullanıcı yetkilerini belirlemeyi ve Trusted Hosts kullanarak yönetici erişimini IP tabanlı olarak sınırlandırmayı öğrendiniz.
