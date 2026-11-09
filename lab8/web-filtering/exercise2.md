# LAB-8 > Statik URL Filtreleme Yapılandırması

# Alıştırma 2: Statik URL Filtreleme Yapılandırması

Bu alıştırmada statik bir URL filtresi yapılandıracak ve güvenlik profilini Flow-based inceleme modunda çalışan bir güvenlik duvarı politikasına uygulayacaksınız. Ardından Web Filter günlük kayıtlarını inceleyeceksiniz.

## Flow-Based İnceleme Modunda Statik URL Filtresi Oluşturma

Bu bölümde statik bir URL filtre kaydı oluşturacak ve inceleme modunu Flow-based olarak değiştireceksiniz.

#### Statik URL filtresi oluşturmak için

1. Local-FortiGate GUI arayüzüne bağlanın ve `admin` kullanıcı adı ile `password` parolasını kullanarak oturum açın.
2. **Security Profiles** > **Web Filter** menüsüne tıklayın.
3. Düzenlemek için **default** Web Filter profiline çift tıklayın.
4. **Static URL Filter** bölümünde **URL Filter** seçeneğini etkinleştirin.
5. **Create New** seçeneğine tıklayın ve aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| URL | `www.bing.com` |
| Type | Simple |
| Action | Block |
| Status | Enable |

6. **OK** seçeneğine tıklayın.

Yapılandırmanız aşağıdaki örnekle aynı olmalıdır:

![Statik URL filtresi yapılandırması](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Web_Filter/Lab8_WF_URL.png)

7. **OK** seçeneğine tıklayın.

#### İnceleme modunu Flow-based olarak değiştirmek için

1. Local-FortiGate GUI arayüzünde **Security Profiles** > **Web Filter** menüsüne tıklayın.
2. Düzenlemek için **default** Web Filter profiline çift tıklayın.
3. **Feature set** alanında **Flow-based** seçeneğini belirleyin.

![Web Filter profilinde Flow-based modunun seçilmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Web_Filter/Lab8_WF_Feature_flow.png)

4. **OK** seçeneğine tıklayın.
5. **Policy & Objects** > **Firewall Policy** menüsüne tıklayın.
6. Düzenlemek için **Full_Access** politikasına çift tıklayın.
7. **Inspection Mode** alanında **Flow-based** seçeneğini belirleyin.

![Güvenlik duvarı politikasında Flow-based inceleme modunun seçilmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_inspection_mode.png)

8. **OK** seçeneğine tıklayın.

#### Statik URL filtresini test etmek için

1. Local-Client sanal makinesinde yeni bir tarayıcı sekmesi açın ve `www.bing.com` adresine erişmeyi deneyin.

Bir uyarı görüntülenir. Bu mesajın daha önce görüntülenen mesajdan farklı olduğuna dikkat edin.

![Statik URL filtresi nedeniyle engellenen web sitesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Web_Filter/Lab8_WF_URL_blocked.png)

> **Durun ve düşünün!**
>
> Engelleme mesajı neden farklı?

FortiGate, statik URL filtresini FortiGuard kategori filtresinden önce uygular. `www.bing.com` adresi URL filtre desenine eşleştiği için artık engellenir ve FortiGate ilgili URL filtreleme mesajını görüntüler.

## Web Filter Günlük Kayıtlarını İnceleme

#### Web Filter günlük kayıtlarını görüntülemek için

1. Local-FortiGate GUI arayüzünde oturum açtığınız tarayıcı sekmesine geri dönün ve **Log & Report** > **Security Events** menüsüne tıklayın.
2. **Summary** bölümünün altında **Web Filter** seçeneğine tıklayın.

Aşağıdakine benzer bilgiler görmelisiniz:

![FortiGuard Web Filter günlük özeti](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Web_Filter/Lab8_WF_Log_FortiGuard.png)

> **Durun ve düşünün!**
>
> `www.bing.com` web sitesi için ilk günlük kaydı neden engellenmiş olarak tanımlanıyor?

Başlangıçta `www.bing.com` web sitesi **Search Engines and Portals** kategorisindedir. Bu kategori **Allow** olarak ayarlanmıştır ve güvenlik günlüğü oluşturmaz.

Bir web sitesine izin verirken aynı zamanda güvenlik günlüğü oluşturmak istiyorsanız kategorinin **Monitor** olarak ayarlanması gerekir.

Günlük kayıtlarına göre `http://www.bing.com` engellenmiştir. Ancak **Proceed** seçeneğine tıklayıp kimlik doğrulaması yaptıktan sonra günlüklerde farklı bir işlem olan **passthrough** görüntülenir.

**Search Engines and Portals** kategorisini **Malicious Websites** olarak geçersiz kıldığınızı ve bu kategorinin önce **Block**, ardından **Authenticate** olarak ayarlandığını unutmayın.

3. Kategori alanı boş olan bir günlük kaydına çift tıklayın.

Aşağıdakine benzer bilgiler görmelisiniz:

![Statik URL filtresine ait günlük kaydı ayrıntıları](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Web_Filter/Lab8_WF_Log_URL.png)

> **Durun ve düşünün!**
>
> Kategori alanı neden boş?

Web sitesi statik URL filtresi tarafından engellendiği için FortiGuard, FortiGuard web derecelendirmesini uygulamaz ve web sitesi için bir kategori bilgisi sağlamaz.

---

**Alıştırma tamamlandı.** Bu alıştırmada statik URL filtresi oluşturdunuz, Web Filter profilini ve güvenlik duvarı politikasını Flow-based inceleme modunda yapılandırdınız ve engelleme işlemlerine ait Web Filter günlük kayıtlarını incelediniz.
