# LAB-7 > Flow-Based Antivirus Taramasının Yapılandırılması

# Alıştırma 1: Flow-Based Antivirus Taramasının Yapılandırılması

Bu alıştırmada, flow-based inceleme modunda çalışan bir antivirus profiline sahip güvenlik duvarı politikası yapılandıracaksınız. Ardından, bir FTP sunucusunda bulunan dosyayı indirmeyi deneyerek yapılandırmayı test edeceksiniz. Son olarak, antivirus taramasıyla ilgili günlük kayıtlarını ve özet bilgilerini inceleyeceksiniz.

## Antivirus Profilinin İnceleme Modunu Yapılandırma

Antivirus profilinde incelenen protokol olarak FTP'nin yapılandırıldığını ve Flow-based modunun seçili olduğunu doğrulayacaksınız.

#### Antivirus profilini doğrulamak için

1. Local-FortiGate GUI arayüzüne bağlanın ve `admin` kullanıcı adı ile `password` parolasını kullanarak oturum açın.
2. **Security Profiles** > **AntiVirus** menüsüne tıklayın.
3. **default** antivirus profiline sağ tıklayın ve **Edit** seçeneğine tıklayın.
4. **Inspected Protocols** bölümünde **FTP** seçeneğinin etkin olduğunu doğrulayın.

![FTP protokolünün kontrol edilmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_FTP.png)

> **Durun ve düşünün!**
>
> **Feature set** alanı neden kullanılamıyor?

Düşük donanım kapasiteli platformlarda bu özellik, yalnızca `gui-proxy-inspection` CLI komutu etkinleştirildikten sonra GUI üzerinden kullanılabilir.

1. Local-FortiGate CLI arayüzüne bağlanın ve `admin` kullanıcı adı ile `password` parolasını kullanarak oturum açın.
2. Aşağıdaki komutları girin:

```shell
config system settings

set gui-proxy-inspection enable

end
```

3. Local-FortiGate GUI arayüzüne geri dönün. Sağ üst köşedeki **admin** seçeneğine tıklayın ve ardından **Logout** seçeneğini seçin.

![Oturumun kapatılması](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_logout.png)

4. `admin` kullanıcı adı ve `password` parolasıyla yeniden oturum açın.
5. **Security Profiles** > **AntiVirus** menüsüne tıklayın.
6. **default** antivirus profiline sağ tıklayın ve **Edit** seçeneğine tıklayın.
7. **Feature set** alanında **Flow-based** seçeneğinin seçili olduğunu doğrulayın.

![Flow-based özelliğinin kontrol edilmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_feature.png)

8. **OK** seçeneğine tıklayın.

## Antivirus Profilini Güvenlik Duvarı Politikasında Etkinleştirme

FortiGate güvenlik duvarı politikalarında varsayılan olarak Flow-based inceleme modu etkindir. Bu bölümde, güvenlik duvarı politikasına antivirus profilini ekleyeceksiniz.

#### Güvenlik duvarı politikasını antivirus profiliyle yapılandırmak için

1. Local-FortiGate GUI arayüzünde **Policy & Objects** > **Firewall Policy** menüsüne tıklayın.
2. Düzenlemek için **Full_Access** politikasına çift tıklayın.
3. **Inspection Mode** alanında **Flow-based** seçeneğinin seçili olduğunu doğrulayın.

![İnceleme modunun kontrol edilmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_inspection_mode.png)

4. **Security Profiles** bölümünde **AntiVirus** seçeneğini etkinleştirin ve profil olarak **default** seçeneğini belirleyin.
5. Diğer ayarları varsayılan değerlerinde bırakın ve değişiklikleri kaydetmek için **OK** seçeneğine tıklayın.

## Flow-Based Antivirus Profilini Test Etme

Flow-based antivirus profilinin çalışmasını FTP üzerinden test edeceksiniz.

#### Antivirus yapılandırmasını test etmek için

1. Local-Client sanal makinesinde masaüstünden FileZilla FTP istemci yazılımını açın.
2. Sol üst köşedeki **Site Manager** simgesine tıklayın ve ardından **Linux** seçeneğini belirleyin.

![Linux FTP sunucusunun seçilmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_linux.png)

3. **Remote site** bölümünde `eicar.com` dosyasına sağ tıklayın ve **Download** seçeneğini seçin.

![EICAR dosyasının indirilmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_download.png)

İstemci tarafında, sunucunun bağlantıyı sonlandırdığına ilişkin bir hata mesajı görüntülenmelidir. FortiGate, sunucu yanıtı olarak bu mesajın yerine geçen yanıtı gönderir.

![Bağlantının sonlandırıldığını gösteren hata mesajı](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/Lab-10-AV-08b.png)

> **Bilgi:**
>
> Flow-based inceleme modunda FortiGate, politikadan geçen trafiği tampon belleğe (buffer) almaz. Trafikte bir ihlal tespit ederse alıcıya bir reset paketi göndererek bağlantıyı sonlandırır. Böylece zararlı içeriğin başarıyla iletilmesini engeller.

4. FileZilla FTP istemcisini kapatın.

## Antivirus Günlük Kayıtlarını Görüntüleme

Günlük kayıtları; ağ trafiğini izlemenize, sorunları tespit etmenize, temel trafik davranışlarını belirlemenize ve gerektiğinde ağ güvenliğinde düzenlemeler yapmanıza yardımcı olur. Bu bölümde antivirus günlük kayıtlarını inceleyeceksiniz.

#### Forward Traffic günlük kayıtlarını görüntülemek için

1. Local-FortiGate GUI arayüzünde **Log & Report** > **Forward Traffic** menüsüne tıklayın.
2. FTP üzerinden dosyaya erişmeye çalıştığınız sırada oluşturulan antivirus günlük kaydını bulun ve ayrıntılarını görüntülemek için ilgili kayıt satırına çift tıklayın.

**Details** sekmesinde, gerçekleştirilen işlemle birlikte iletilen trafik günlük kayıtlarına ilişkin bilgiler görüntülenir.

![Forward Traffic kayıtlarının ayrıntıları](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_Details.png)

3. **Security** sekmesine tıklayın.

**Security** sekmesinde virüsle ilgili bilgiler görüntülenir.

![Güvenlik ayrıntılarının görüntülenmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_security.png)

#### Security Events günlük kayıtlarını görüntülemek için

1. Local-FortiGate GUI arayüzünde **Log & Report** > **Security Events** > **AntiVirus** menüsüne tıklayın.

![Antivirus güvenlik olaylarının görüntülenmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_events.png)

> **Bilgi:**
>
> Günlük kayıtlarını görebilmek için arama çubuğundaki filtreleri temizlemeniz ve zaman aralığını 1 saate çıkarmanız gerekebilir.

2. FTP üzerinden dosyaya erişmeye çalıştığınız sırada oluşturulan antivirus günlük kaydını bulun ve güvenlik ayrıntılarını görüntülemek için ilgili kayıt satırına çift tıklayın.

![Antivirus günlük kaydının ayrıntıları](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/AV/Lab7_AV_Flow_log.png)

---

**Alıştırma tamamlandı.** Bu alıştırmada Flow-based antivirus profilini yapılandırdınız, FTP üzerinden EICAR test dosyasını indirmeyi deneyerek tarama mekanizmasını test ettiniz ve oluşan trafik ile güvenlik günlük kayıtlarını incelediniz.
