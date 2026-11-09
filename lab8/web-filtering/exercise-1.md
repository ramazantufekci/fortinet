# LAB-8 > FortiGuard Web Filtering Yapılandırması

# Alıştırma 1: FortiGuard Web Filtering Yapılandırması

FortiGuard kategorilerine göre web filtreleme yapabilmek için öncelikle FortiGate üzerinde geçerli bir FortiGuard güvenlik aboneliği lisansı bulunduğundan emin olmanız gerekir. Bu lisans, uygun olmayan web sitelerine karşı koruma sağlamak için gerekli web filtreleme özelliklerini sunar.

Ardından FortiGate üzerinde kategori tabanlı bir Web Filter güvenlik profili yapılandırmalı ve HTTP trafiğini denetlemek için bu güvenlik profilini bir güvenlik duvarı politikasına uygulamalısınız.

Son olarak, web sitelerinin derecelendirmelerine göre FortiGate tarafından gerçekleştirilen farklı işlemleri test edebilirsiniz.

## FortiGate Ayarlarını İnceleme

Bu bölümde, geri yüklenen yapılandırmaya göre inceleme modunu ve lisans durumunu kontrol edeceksiniz. Ayrıca FortiGate'in web filtreleme isteklerini göndermek için kullandığı FortiGuard Distribution Server (FDS) sunucularını listeleyeceksiniz.

#### FortiGate üzerindeki geri yüklenmiş ayarları incelemek için

1. Local-FortiGate GUI arayüzüne bağlanın ve `admin` kullanıcı adı ile `password` parolasını kullanarak oturum açın.
2. Dashboard üzerinde **Licenses** bileşenini bulun. **Web Filter** üzerine gelerek hizmetin lisanslı ve etkin olduğunu doğrulayın.

Aşağıdakine benzer bilgiler görmelisiniz.

Yapılandırma dosyasının geri yüklenmesinin ardından FortiGate yeniden başlatıldığı için Web Filter lisans durumu **Unavailable** olarak görünebilir. Bu durumda:

1. **System** > **FortiGuard** menüsüne gidin.
2. **Filtering** bölümünde **Test Connectivity** seçeneğine tıklayarak güncellemeyi zorlayın.
3. Onaylamak için **OK** seçeneğine tıklayın.

Aynı zamanda **Web Filter cache** özelliğinin etkin olduğunu da doğrulayabilirsiniz.

4. **Policy & Objects** > **Firewall Policy** menüsüne tıklayın.
5. Düzenlemek için **Full_Access** politikasına çift tıklayın.
6. **Inspection Mode** ayarını kontrol edin.

Varsayılan inceleme modunun **Flow-based** olarak ayarlandığını göreceksiniz.

7. **Inspection Mode** alanında **Proxy-based** seçeneğini belirleyin.
8. **OK** seçeneğine tıklayın.

## Web Filter Kategorilerini Belirleme

Web Filter kategorilerini yapılandırmadan önce FortiGuard Web Filtering hizmetinin belirli web sitelerini hangi kategoriler altında sınıflandırdığını belirlemelisiniz.

#### Web Filter kategorilerini belirlemek için

1. Local-Client sanal makinesinde yeni bir tarayıcı sekmesi açın ve aşağıdaki adrese gidin:

   https://www.fortiguard.com/webfilter

2. **Web Filter Lookup** aracını kullanarak aşağıdaki URL'yi aratın:

   `www.facebook.com`

Bu, Web Filter yapılandırmanızı test etmek için daha sonra kullanacağınız web sitelerinden biridir.

Gördüğünüz gibi Facebook, **Social Networking** kategorisinde listelenmektedir.

3. **Web Filter Lookup** aracını kullanarak aşağıdaki web sitelerinin kategorilerini de bulun:

   - `www.skype.com`
   - `www.ask.com`
   - `www.bing.com`

Web Filter yapılandırmanızı bu web siteleri üzerinden de test edeceksiniz.

Aşağıdaki tabloda her URL için atanan kategori ve Web Filter güvenlik profilinize göre FortiGate üzerinde yapılandıracağınız işlem gösterilmektedir.

| Web sitesi | Kategori | İşlem |
|---|---|---|
| `www.facebook.com` | Social Networking | Block |
| `www.skype.com` | Internet Telephony | Warning |
| `www.bing.com` | Search Engines and Portals | Allow |
| `www.ask.com` | Search Engines and Portals | Allow |

## FortiGuard Kategori Tabanlı Web Filter Yapılandırması

Öncelikle varsayılan Web Filter profilini inceleyecek, ardından FortiGuard kategori tabanlı filtreyi yapılandıracaksınız.

#### Web Filter güvenlik profilini yapılandırmak için

1. Local-FortiGate GUI arayüzüne geri dönün ve **Security Profiles** > **Web Filter** menüsüne tıklayın.
2. Düzenlemek için **default** Web Filter profiline çift tıklayın.
3. **FortiGuard Category Based Filter** özelliğinin etkin olduğunu doğrulayın.

Bir kategoriyi genişletmek için **+**, daraltmak için **-** simgesine tıklayabilirsiniz.

4. Her kategori için tanımlanmış varsayılan işlemleri inceleyin.

| Kategori | İşlem |
|---|---|
| Local Categories | Disable |
| Potentially Liable | Extremist Group: Block; diğer tüm alt kategoriler: Allow |
| Adult/Mature Content | Block |
| Bandwidth Consuming | Allow |
| Security Risk | Block |
| General Interest - Personal | Allow |
| General Interest - Business | Allow |
| Unrated | Block |

**Not:** Alt kategorileri görüntülemek için **Potentially Liable** kategorisini genişletin.

5. **General Interest - Personal** kategorisini genişleterek alt kategorilerini görüntüleyin.
6. **Social Networking** üzerine sağ tıklayın ve **Block** seçeneğini belirleyin.
7. **Bandwidth Consuming** kategorisini genişleterek alt kategorilerini görüntüleyin.
8. **Internet Telephony** üzerine sağ tıklayın ve **Warning** seçeneğini belirleyin.

**Edit Filter** penceresi açılır. Bu pencerede uyarı aralığını değiştirebilirsiniz.

9. Varsayılan **5 minutes** değerini koruyun ve **OK** seçeneğine tıklayın.
10. **OK** seçeneğine tıklayın.

## Web Filter Profilini Güvenlik Duvarı Politikasına Uygulama

Web Filter profilini yapılandırdığınıza göre web trafiğini denetlemeye başlamak için bu güvenlik profilini bir güvenlik duvarı politikasına uygulamanız gerekir.

Ayrıca web trafiğinin oluşturduğu güvenlik olaylarını kaydetmek ve analiz etmek için günlük kaydını etkinleştireceksiniz.

> **Uzmanlık Görevi!**
>
> Local-FortiGate GUI arayüzünde Web Filter profilini mevcut **Full_Access** güvenlik duvarı politikasına uygulayın.
>
> Günlük kaydının da etkin olduğundan ve **Security Events** olarak ayarlandığından emin olun.

Yardıma ihtiyacınız varsa veya yaptığınız işlemleri doğrulamak istiyorsanız aşağıdaki adım adım talimatları izleyebilirsiniz.

Görevi tamamladıktan sonra **Web Filter'ı Test Etme** bölümüne geçin.

#### Bir güvenlik profilini güvenlik duvarı politikasına uygulamak için

1. Local-FortiGate GUI arayüzünde **Policy & Objects** > **Firewall Policy** menüsüne tıklayın.
2. Düzenlemek için **Full_Access** politikasına çift tıklayın.
3. **Security Profiles** bölümünde **Web Filter** seçeneğini etkinleştirin ve profil olarak **default** seçeneğini belirleyin.
4. **SSL Inspection** alanının yanında görünen uyarı simgesinin üzerine gelin.

Aşağıdakine benzer bir mesaj görüntülenmelidir.

5. **SSL Inspection** alanında **certification-inspection** seçeneğini belirleyin.

**Not:** Web filtreleme, URL bilgisine ihtiyaç duyar ancak içeriğin tamamını incelemez. Bu nedenle **deep-inspection** yerine **certification-inspection** seçeneğini kullanabilirsiniz.

6. **Log Allowed Traffic** alanında **Security Events** seçeneğinin seçili olduğundan emin olun.
7. Diğer tüm ayarları varsayılan değerlerinde bırakın ve **OK** seçeneğine tıklayın.

## Web Filter'ı Test Etme

Bu bölümde, her kategori için yapılandırdığınız Web Filter güvenlik profilini test edeceksiniz.

#### Web Filter'ı test etmek için

1. Local-FortiGate CLI arayüzünde `admin` kullanıcı adı ve `password` parolasıyla oturum açın.
2. Web Filter durumunu doğrulamak için aşağıdaki komutu girin:

```shell
get webfilter status
```

`get webfilter status` ve `diagnose debug rating` komutları, FortiGate'in web filtreleme isteklerini göndermek için kullandığı FDS sunucularının listesini gösterir.

Normal çalışma koşullarında FortiGate, derecelendirme isteklerini yalnızca listedeki ilk sunucuya gönderir. Her sunucunun gidiş-dönüş süresi (RTT), 2 dakikada bir kontrol edilir.

> **Durun ve düşünün!**
>
> Ağınızdan yalnızca bir IP adresi neden sunucu listesinde görünüyor?

Laboratuvar ortamınızda `10.0.1.241` IP adresinde çalışan ve yerel FDS olarak yapılandırılmış bir FortiManager bulunmaktadır. Bu sunucu, FDS web derecelendirme veritabanının yerel bir kopyasını içerir.

FortiGate, derecelendirme isteklerini genel FDS sunucularına göndermek yerine FortiManager'a gönderir. Bu nedenle komut çıktısında yalnızca FortiManager IP adresi listelenir.

3. Local-Client sanal makinesinde yeni bir tarayıcı sekmesi açın ve `www.facebook.com` adresine gidin.

Bu web sitesi kategorisi için önceden tanımlanmış işleme uygun olarak bir uyarı görüntülenir.

4. Yeni bir tarayıcı sekmesi açın ve `www.skype.com` adresine gidin.

Bu web sitesi kategorisi için önceden tanımlanmış işleme uygun olarak bir uyarı görüntülenir.

5. Uyarıyı kabul etmek ve web sitesine erişmek için **Proceed** seçeneğine tıklayın.
6. Yeni bir tarayıcı sekmesi açın ve `www.bing.com` adresine gidin.

Bu web sitesi, **Allow** olarak ayarlanmış **Search Engines and Portals** kategorisinde bulunduğu için açılır.

7. Local-Client sanal makinesindeki tarayıcı sekmelerini kapatın.

## Web Rating Override Oluşturma

Bu bölümde `www.bing.com` web sitesinin kategori derecelendirmesini değiştireceksiniz.

#### Web Rating Override oluşturmak için

1. Local-FortiGate GUI arayüzüne geri dönün ve **Security Profiles** > **Web Rating Overrides** menüsüne tıklayın.
2. **Create New** seçeneğine tıklayın ve aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| URL | `www.bing.com` |
| Category | Security Risk |
| Sub-Category | Malicious Websites |

3. **OK** seçeneğine tıklayın.

## Web Rating Override İşlemini Test Etme

Önceki bölümde oluşturduğunuz Web Rating Override yapılandırmasını test edeceksiniz.

#### Web Rating Override işlemini test etmek için

1. Local-Client sanal makinesinde yeni bir tarayıcı sekmesi açın ve `www.bing.com` web sitesine yeniden erişmeyi deneyin.

Web sitesi engellenir ve FortiGuard derecelendirmesi yerine yerel derecelendirme kullanılır.

> **Durun ve düşünün!**
>
> `www.bing.com` web sitesi neden engelleniyor?

Web Rating Override işlemi, web sitesinin kategorisini değiştirir. Güvenlik duvarı politikasında uygulanan varsayılan Web Filter profilinde **Malicious Websites** kategorisi **Block** olarak ayarlanmıştır.

Sonuç olarak `www.bing.com` web sitesi artık engellenmektedir.

## Authenticate İşlemini Yapılandırma

Bu bölümde FortiGuard kategorilerinden **Malicious Websites** için uygulanacak işlemi **Authenticate** olarak ayarlayacaksınız. Ardından Authenticate işlemini test etmek için bir kullanıcı tanımlayacaksınız.

#### Authenticate işlemini yapılandırmak için

1. Local-FortiGate GUI arayüzünde **Security Profiles** > **Web Filter** menüsüne tıklayın.
2. Düzenlemek için **default** Web Filter profiline çift tıklayın.
3. **FortiGuard Category Based Filter** bölümünde **Security Risk** kategorisini genişletin.
4. **Malicious Websites** üzerine sağ tıklayın ve **Authenticate** seçeneğini belirleyin.

**Edit Filter** penceresi açılır. Bu pencerede uyarı aralığını değiştirebilir ve kullanıcı gruplarını seçebilirsiniz.

5. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Warning Interval | 5 minutes |
| Selected User Groups | Override_Permissions |

6. **OK** seçeneğine tıklayın.
7. Yeniden **OK** seçeneğine tıklayın.

**Not:** Bu laboratuvar için `Override_Permissions` önceden tanımlanmış bir kullanıcı grubudur. Kullanıcı gruplarını incelemek için **User & Authentication** > **User Groups** menüsüne tıklayın.

#### Kullanıcı oluşturmak için

1. Local-FortiGate GUI arayüzünde **User & Authentication** > **User Definition** menüsüne tıklayın.
2. **Create New** seçeneğine tıklayın.
3. **User Type** alanında **Local User** seçeneğini belirleyin.
4. **Next** seçeneğine tıklayın ve aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Username | `student` |
| Password | `fortinet` |

5. **Next** seçeneğine tıklayın.
6. Tekrar **Next** seçeneğine tıklayın.
7. **User Group** seçeneğini etkinleştirin ve **Override_Permissions** grubunu seçin.
8. **Submit** seçeneğine tıklayın.

`student` kullanıcısı oluşturulmuştur.

#### Authenticate işlemini test etmek için

1. Local-Client sanal makinesinde yeni bir tarayıcı sekmesi açın ve `www.bing.com` adresine erişmeyi deneyin.

Bir uyarı görüntülenir. Bu mesajın daha önce görüntülenen uyarı mesajından farklı olduğuna dikkat edin.

2. **Proceed** seçeneğine tıklayın.

**Not:** Bu aşamada bir sertifika uyarısı alabilirsiniz. Bu normaldir ve kendinden imzalı (self-signed) bir sertifika kullanılmasından kaynaklanır. İşlemin geri kalanına devam etmek için uyarıyı kabul edin:

- **Advanced** seçeneğine tıklayın.
- **Accept the Risk and Continue** seçeneğini belirleyin.

3. Aşağıdaki kimlik bilgilerini girin:

| Alan | Değer |
|---|---|
| Username | `student` |
| Password | `fortinet` |

4. **Continue** seçeneğine tıklayın.

Artık `www.bing.com` web sitesi düzgün şekilde görüntülenir.

5. Local-Client sanal makinesindeki tarayıcı sekmelerini kapatın.

---

**Alıştırma tamamlandı.** Bu laboratuvarda FortiGuard kategori tabanlı Web Filter yapılandırmasını, güvenlik duvarı politikasına profil uygulamayı, Web Rating Override oluşturmayı ve Authenticate işlemiyle kullanıcı doğrulamasını test ettiniz.
