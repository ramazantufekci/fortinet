# LAB-7: SSL Sertifikalarındaki Anormalliklerin Yönetilmesi (Dealing With Anomalies)

Sertifikalarla çalışırken geçersiz veya iptal edilmiş sertifikalardan kaynaklanan çeşitli sorunlarla karşılaşabilirsiniz. Ayrıca Full SSL Inspection kullanımını engelleyen bazı kısıtlamalarla da ilgilenmeniz gerekebilir.

Bu laboratuvar çalışmasında öncelikle FortiGate GUI üzerinden bir Sertifika İptal Listesini (Certificate Revocation List - CRL) içe aktarmayı öğreneceksiniz.

Ardından FortiGate'in, Deep Inspection SSL profiliyle eşleşen trafik içerisinde geçersiz sertifikalarla karşılaştığında nasıl tepki verdiğini inceleyeceksiniz.

Son olarak belirli bir web sitesini Full SSL Inspection kapsamı dışında bırakmak için bir istisna (Exception) yapılandıracaksınız.

---

## 1. Geçersiz Sertifikaları Yönetme (Manage Invalid Certificates)

Bir sertifika aşağıdaki nedenlerden dolayı geçersiz olabilir:

- Sertifikanın geçerlilik süresinin dolması.
- Sertifikayı imzalayan sertifika otoritesinin (CA) sertifikayı iptal etmesi.

Bir şirket; sertifikanın ele geçirilmesi, özel anahtarın kaybolması veya sertifikanın sahibi olan çalışanın şirketten ayrılması gibi nedenlerle bir sertifikayı iptal etmek isteyebilir.

İptal edilen sertifikaları diğer sistemlere bildirmek amacıyla CA yöneticileri belirli aralıklarla CRL (Certificate Revocation List) yayımlar.

Bu bölümde FortiGate üzerine bir CRL aktaracaksınız.

### 1.1. CRL İçe Aktarma (Import a CRL)

1. Local-FortiGate GUI arayüzüne bağlanın.
2. Aşağıdaki bilgilerle oturum açın:
   - **Kullanıcı adı:** `admin`
   - **Parola:** `password`
3. **System > Certificates** menüsüne gidin.
4. **Remote CA Certificate** bölümünde bulunan `Fortinet_Wifi_CA` sertifikasına sağ tıklayın.
5. **View Details** seçeneğini seçin.
6. **Extensions** bölümüne ilerleyin.
7. **X509v3 CRL Distribution Points** alanını bulun.
8. Listelenen URI adreslerinden birini seçin.
9. Dağıtım noktası adresini kopyalamak için `Ctrl + C` tuşlarına basın.

![CRL dağıtım noktası URI adresi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-CRL-URI.png)

10. **Certificate Details** penceresinden çıkmak için **Close** butonuna tıklayın.
11. **Create/Import > CRL** menüsüne gidin.

![CRL içe aktarma menüsü](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-import-crl.png)

12. **HTTP** seçeneğini etkinleştirin.
13. Bir önceki adımda kopyaladığınız CRL HTTP sunucusunun URI adresini ilgili alana yapıştırın.

![CRL URI adresinin yapılandırılması](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-CRL-import.png)

14. **OK** butonuna tıklayın.

FortiGate GUI üzerinde işlemin kabul edildiğini belirten kısa bir bilgilendirme mesajı görüntülenecektir.

![CRL ekleme onay mesajı](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-CRL-addition-ack.png)

15. Birkaç saniye bekleyin.
16. Sayfayı yenilemek için tekrar **System > Certificates** menüsüne gidin.

CRL bölümü, yeni eklediğiniz CRL kaydını içerecektir.

![FortiGate sertifika listesinde CRL kaydı](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-certificate-list-with-crl.png)

> **Önemli:** FortiGate üzerinde yalnızca FortiGate'in güvendiği bir CA'ya ait CRL dosyalarını yükleyebilirsiniz. Şirketinizin CA sertifikasına karşılık gelen CRL dosyasını yüklemek istiyorsanız öncelikle şirketinizin CA sertifikasını FortiGate üzerine yüklemelisiniz.

### 1.2. OCSP Yapılandırması

**Online Certificate Status Protocol (OCSP)**, X.509 dijital sertifikaların iptal durumunu öğrenmek için kullanılan bir protokoldür.

OCSP, sertifika iptal durumunu kontrol etmek için CRL'ye alternatif olarak kullanılabilir.

FortiGate üzerinde OCSP varsayılan olarak devre dışıdır.

Bu laboratuvar çalışmasında, OCSP destekleyen bilinen sertifika otoritelerinden sertifika doğrulama bilgilerini alabilmek için OCSP özelliğini CLI komutlarıyla etkinleştireceksiniz.

FortiGate CLI üzerinde aşağıdaki komutları çalıştırın:

```shell
config vpn certificate setting

set ocsp-option certificate

set ocsp-status enable

set strict-ocsp-check enable

end
```

Komutların işlevleri:

| Komut | Açıklama |
|---|---|
| `set ocsp-option certificate` | OCSP kontrolünün sertifika doğrulaması için kullanılmasını yapılandırır. |
| `set ocsp-status enable` | OCSP durum kontrolünü etkinleştirir. |
| `set strict-ocsp-check enable` | OCSP kontrolünde katı doğrulama uygulanmasını etkinleştirir. |

---

## 2. Full SSL Inspection ile Geçersiz Sertifikaları Engelleme

Bu bölümde FortiGate'in süresi dolmuş veya iptal edilmiş sertifikalara sahip web sitelerine erişimi nasıl engellediğini test edeceksiniz.

### 2.1. Geçersiz Sertifikaları Engelleme Ayarlarını Kontrol Etme

1. Local-FortiGate GUI arayüzüne geri dönün.
2. **Policy & Objects > Firewall Policy** menüsüne gidin.
3. `Full_Access` politikasını seçin ve **Edit** butonuna tıklayın.
4. **Security Profiles** bölümüne ilerleyin.
5. **SSL Inspection** profilinin yanındaki kalem simgesine tıklayarak `Custom_Full_Inspection` profilini düzenleyin.
6. Ayarların aşağıdaki görselde gösterilen yapılandırmayla aynı olduğunu doğrulayın.

![SSL/SSH Inspection sertifika ayarları](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-ssl-ssh-certificates-settings.png)

7. Herhangi bir değişiklik yapmayın.
8. SSL Inspection profilinden çıkmak için **Cancel** butonuna tıklayın.
9. Güvenlik duvarı politikası yapılandırmasından çıkmak için tekrar **Cancel** butonuna tıklayın.

### 2.2. Geçersiz Sertifikaya Sahip Web Sitelerini Test Etme

1. Local-Client VM üzerine bağlanın.
2. Aşağıdaki bilgilerle oturum açın:
   - **Kullanıcı adı:** `Administrator`
   - **Parola:** `password`
3. Bir tarayıcı açın.
4. Aşağıdaki web sitesini ziyaret edin:

[https://revoked.badssl.com/](https://revoked.badssl.com/)

Bu web sitesi iptal edilmiş bir sertifika kullanmaktadır.

5. Başka bir tarayıcı sekmesi açın ve aşağıdaki adresi ziyaret edin:

[https://expired.badssl.com/](https://expired.badssl.com/)

Bu web sitesi ise geçerlilik süresi dolmuş bir sertifika kullanmaktadır.

FortiGate, bu web sitelerine erişimi engelleyecek ve tarayıcıda aşağıdaki görsele benzer bir uyarı mesajı görüntülenecektir.

![Geçersiz sertifika nedeniyle tarayıcıda görüntülenen uyarı](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-warning-message-browser.png)

---

## 3. SSL Günlük Kayıtlarını İnceleme

FortiGate'in geçersiz sertifikalar nedeniyle engellediği bağlantıları güvenlik olay günlüklerinden inceleyebilirsiniz.

### 3.1. SSL Log Kayıtlarını Görüntüleme

1. Local-FortiGate GUI arayüzüne geri dönün.
2. **Log & Report > Security Events** menüsüne gidin.
3. SSL günlük kayıtlarını görüntülemek için **SSL** bileşenini genişletin.

FortiGate'in ilgili web sitelerine erişimi engellediğini görebilirsiniz.

![SSL nedeniyle engellenen trafik günlükleri](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ssl-log-blocked.png)

4. Ayrıntıları görüntülemek için bir günlük kaydına çift tıklayın.

![SSL log ayrıntıları](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Cert_Ops/ex3-ssl-log-details.png)

Süresi dolmuş ve iptal edilmiş sertifikalar için oluşturulan günlük mesajlarının benzer olduğunu görebilirsiniz.

---

## 4. Full SSL Inspection İçin İstisna Tanımlama

Sertifika değiştirme işlemi bazı kullanıcıların belirli web sitelerine erişmesini engelleyebilir.

Bu gibi durumlarda belirli web sitelerini Full SSL Inspection kapsamı dışında bırakmak için istisnalar (Exceptions) tanımlayabilirsiniz.

Ayrıca yasal düzenlemeler nedeniyle bazı web sitelerini veya web sitesi kategorilerini Full SSL Inspection kapsamı dışında bırakmanız gerekebilir.

Örneğin bazı ülkelerde kullanıcılar ile finans kuruluşlarının sunucuları arasındaki trafiğin Deep Inspection yöntemiyle incelenmesi yasaklanmış olabilir.

Bu bölümde daha önce oluşturduğunuz SSL/SSH Deep Inspection profiline bir web sitesi istisnası ekleyeceksiniz.

### 4.1. SSL Full Inspection İçin Web Sitesi İstisnası Oluşturma

1. Local-FortiGate GUI arayüzüne bağlanın.
2. Aşağıdaki bilgilerle oturum açın:
   - **Kullanıcı adı:** `admin`
   - **Parola:** `password`
3. **Security Profiles > SSL/SSH Inspection** menüsüne gidin.
4. `Custom_Full_Inspection` profilini düzenleyin.
5. **Exempt from SSL Inspection** bölümüne ilerleyin.
6. Yeni bir adres nesnesi (Address Object) oluşturmak için **+** simgesine tıklayın.

Aşağıdaki parametrelerle yeni bir adres nesnesi oluşturun:

| Alan | Değer |
|---|---|
| Name | `badssl` |
| Type | `Subnet` |
| IP/Netmask | `104.154.89.105/32` |

7. **OK** butonuna tıklayın.
8. SSL/SSH Inspection profili içerisinde yeni oluşturduğunuz `badssl` adres nesnesini seçin.
9. SSL/SSH Inspection profilindeki yapılandırma değişikliklerini kaydetmek için **OK** butonuna tıklayın.
10. **Policy & Objects > Firewall Policy** menüsüne gidin.
11. `Full_Access` politikasını düzenleyin.
12. SSL Inspection profili olarak `Custom_Full_Inspection` seçildiğini doğrulayın.
13. **OK** butonuna tıklayarak yapılandırmayı kaydedin.

---

## 5. SSL Full Inspection İstisnasını Test Etme

### 5.1. İstisna Tanımlanan Web Sitelerine Erişimi Kontrol Etme

1. Local-Client VM üzerine geçin.
2. Daha önce test ettiğiniz web sitelerinden birini ziyaret edin:

- [https://revoked.badssl.com/](https://revoked.badssl.com/)
- [https://expired.badssl.com/](https://expired.badssl.com/)

3. Tarayıcıda sertifika uyarısı görüntülenirse:
   - **Advanced** seçeneğine tıklayın.
   - **Accept the Risk and Continue** seçeneğini seçerek tarayıcı uyarısını kabul edin.

Artık web sitesine erişebilirsiniz.

> **Önemli:** Gerçek ortamlarda Full SSL Inspection istisnaları genellikle MITM yöntemini desteklemeyen ve kurum tarafından güvenilen web siteleri için tanımlanır. Bu web sitelerinin normalde geçerli bir sertifikaya sahip olması ve tarayıcıda sertifika uyarısına neden olmaması beklenir.

---

## 6. Laboratuvar Çalışmasının Sonucu

Bu laboratuvar çalışmasında FortiGate üzerinde geçersiz ve iptal edilmiş SSL sertifikalarının nasıl yönetildiğini ve SSL Inspection istisnalarının nasıl yapılandırıldığını öğrendiniz.

Gerçekleştirilen işlemler:

- FortiGate üzerinde CRL içe aktarıldı.
- Sertifika iptal durumunu kontrol etmek için OCSP yapılandırıldı.
- Geçersiz sertifikaya sahip web siteleri test edildi.
- Süresi dolmuş sertifikaların tespit edilmesi sağlandı.
- İptal edilmiş sertifikaların tespit edilmesi sağlandı.
- FortiGate SSL güvenlik günlükleri incelendi.
- `Custom_Full_Inspection` profiline web sitesi istisnası eklendi.
- `badssl` isimli adres nesnesi oluşturuldu.
- Belirtilen web sitesi için SSL Full Inspection istisnası test edildi.

**Sonuç:** FortiGate, Full SSL Inspection kapsamında geçersiz veya iptal edilmiş sertifikalara sahip web sitelerine erişimi engelleyebilmektedir. Ayrıca belirli web siteleri için SSL Inspection istisnası tanımlanarak bu sitelerin derin SSL incelemesinin dışında tutulması sağlanabilir.

---

## 7. Önemli Güvenlik Notları

- CRL dosyalarının güncel tutulması, iptal edilmiş sertifikaların tespit edilmesi açısından önemlidir.
- OCSP ve CRL, sertifika iptal durumunun kontrol edilmesi için kullanılan farklı mekanizmalardır.
- OCSP sunucusuna erişilemiyorsa sertifika doğrulama davranışı FortiGate yapılandırmasına bağlıdır.
- `strict-ocsp-check` ayarı, OCSP doğrulamasının başarısız olduğu durumlarda bağlantıların nasıl değerlendirileceğini etkileyebilir.
- SSL Inspection istisnaları mümkün olduğunca sınırlı tutulmalıdır.
- Bir web sitesini istisna kapsamına almadan önce güvenilirliği ve sertifika durumu kontrol edilmelidir.
- IP adresi üzerinden tanımlanan istisnalar, aynı IP adresini paylaşan farklı alan adlarını da etkileyebilir. Bu nedenle gerçek ortamlarda adres nesnesinin kapsamı dikkatle değerlendirilmelidir.

**Not:** Bu laboratuvarda kullanılan `104.154.89.105/32` adresi eğitim senaryosuna aittir. Gerçek ortamda istisna tanımlarken hedef web sitesinin güncel IP adresini ve kullanılan adres nesnesinin kapsamını doğrulayın.
