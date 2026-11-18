# LAB-12 – Exercise 2: SD-WAN Yapılandırmasının İzlenmesi

Bu alıştırmada öncelikle Local-Client VM üzerinden internet trafiği oluşturacaksınız. Ardından FortiGate GUI üzerinde bulunan SD-WAN araçlarını kullanarak DIA trafik dağılımını, trafik kayıtlarını ve sistem olaylarını inceleyeceksiniz.

## 1. Local-Client VM Üzerinden İnternet Trafiği Oluşturma

Bu adımda bir script kullanarak Local-Client VM üzerinden internet trafiği oluşturacaksınız.

### İnternet trafiği oluşturmak için

1. Local-Client VM üzerinde aşağıdaki bilgilerle oturum açın:
   - **Username:** `Administrator`
   - **Password:** `password`

2. Bir terminal penceresi açın ve aşağıdaki komutları sırasıyla çalıştırın:

```bash
cd Desktop
```

```bash
sh traffic-generation.sh
```

Komutları çalıştırdığınızda terminal çıktısı aşağıdaki örneğe benzer olacaktır.

![Traffic Generation Script](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-script-launch.png)

<aside>
💡 **Bilgi:** Local-Client VM farklı türlerde internet trafiği oluşturmaya başlayacaktır.
</aside>

Script'in tamamlanması yaklaşık 2 dakika sürer. İşlem tamamlandığında terminal penceresinde aşağıdaki mesaj görüntülenir:

```text
Script was successfully completed
```

Script çalışırken bir sonraki adıma geçebilirsiniz.

## 2. DIA Trafik Dağılımını İzleme

Bu bölümde FortiGate GUI üzerindeki SD-WAN sayfasını kullanarak DIA trafik dağılımını izleyecek, ardından trafik kayıtlarını inceleyeceksiniz.

### DIA trafik dağılımını izlemek için

1. Local-FortiGate GUI üzerinde aşağıdaki bilgilerle oturum açın:
   - **Username:** `admin`
   - **Password:** `password`

2. **Network > SD-WAN** menüsüne gidin.

3. **SD-WAN Zones** sekmesine tıklayın.

4. SD-WAN üyelerinin bant genişliği kullanımına göre trafik dağılımını görüntülemek için **Bandwidth** seçeneğine tıklayın.

![SD-WAN Member Usage](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-sd-wan-member-usage.png)

<aside>
💡 **Bilgi:** Trafik dağılımı sizin ekranınızda örnektekinden farklı olabilir.
</aside>

5. Her grafiğin üzerine gelerek ilgili SD-WAN üyesinin (`port1` ve `port2`) kullandığı bant genişliğini görüntüleyin.

6. **Volume** ve **Sessions** grafiklerine tıklayarak diğer trafik istatistiklerini inceleyin.

### Performance SLA değerlerini izleme

1. **Performance SLAs** sekmesine tıklayın.

2. **Latency** seçeneğine tıklayın.

3. Açılan grafikte `Level3_DNS` isimli Performance SLA için son 10 dakika içerisinde ölçülen gecikme (latency) değerlerini inceleyin.

![Performance SLA Graph](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-perf-sla-graph.png)

4. **Packet Loss**, **Latency** ve **Jitter** sütunlarında `port1` ve `port2` yanındaki yeşil okları inceleyin.

Bu oklar, ilgili portun aktif olduğunu ve ölçülen performans değerlerinin kabul edilebilir sınırlar içerisinde bulunduğunu gösterir.

5. **Packet Loss** ve **Jitter** grafiklerine de tıklayarak bu değerleri inceleyin.

## 3. SD-WAN Trafik Loglarını Görüntüleme

Bu bölümde oluşturulan internet trafiğinin hangi SD-WAN kuralları üzerinden geçtiğini ve hangi fiziksel arayüzü kullandığını trafik logları üzerinden kontrol edeceksiniz.

### SD-WAN trafik loglarını görüntülemek için

1. Öncelikle Local-Client VM üzerindeki terminal penceresine dönün ve script'in başarıyla tamamlandığını doğrulayın.

Terminal penceresinde aşağıdaki mesajı görmelisiniz:

```text
The traffic was successfully generated
```

![Traffic Generation Completed](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-script-completed.png)

2. Local-FortiGate GUI üzerinde **Log & Report > Forward Traffic** menüsüne gidin.

3. Log tablosunun sol üst köşesine gelerek sütun ayarları simgesini görüntüleyin.

4. Görüntülenen dişli simgesine tıklayın.

5. Aşağıdaki sütunları seçin:
   - **Destination Interface**
   - **SD-WAN Quality**
   - **SD-WAN Rule Name**

6. **Apply** seçeneğine tıklayın.

7. Log görüntüleme zaman aralığını **1 hour** olarak ayarlayın.

8. Log tablosunu sadeleştirmek için DNS loglarından birinin üzerindeki **DNS** ifadesine sağ tıklayın ve **Not exact match** filtresini seçin.

![DNS Filter](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-DNS-filter.png)

Log tablosu aşağıdaki örneğe benzer olacaktır.

![SD-WAN Traffic Logs](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-SD-WAN-traffic-log.png)

9. Log tablosunu inceleyerek aşağıdaki sonuçları doğrulayın:

| Trafik | SD-WAN Rule Name | Destination Interface |
|---|---|---|
| Dropbox | `Critical-DIA` | `port1` |
| Facebook | `Non-Critical-DIA` | `port2` |
| Pinterest | `Non-Critical-DIA` | `port2` |

Böylece yapılandırdığınız SD-WAN kurallarının ilgili uygulama ve trafik kategorilerine doğru şekilde uygulandığını doğrulamış olursunuz.

### Dur ve düşün!

**Diğer tüm trafikler (DNS, HTTP_BROWSER vb.) için log kayıtlarında neden SD-WAN Quality ve SD-WAN Rule Name sütunlarında herhangi bir bilgi görüntülenmiyor?**

**Cevap:**

Bu trafikler yapılandırılmış SD-WAN kurallarından herhangi biriyle eşleşmemektedir. Bu nedenle FortiGate, söz konusu trafiği **implicit SD-WAN rule** üzerinden yönlendirir.

Implicit SD-WAN rule, oturumları kaynak ve hedef IP adreslerine ve FIB (Forwarding Information Base) içeriğine göre yük dengeleme yöntemiyle dağıtır.

Bu nedenle yapılandırılmış özel SD-WAN kurallarına ait bilgiler ilgili loglarda görüntülenmez.

## 4. SD-WAN Sistem Olaylarını Kontrol Etme

Bu bölümde FortiGate GUI üzerindeki **System Events** log sayfasını kullanarak SD-WAN çalışma durumuyla ilgili olayları inceleyeceksiniz.

### SD-WAN sistem olaylarını izlemek için

1. Local-FortiGate GUI üzerinde **Network > Interfaces** menüsüne gidin.

2. `port1` arayüzüne sağ tıklayın.

3. **Set Status > Disable** seçeneğini kullanarak `port1` arayüzünü devre dışı bırakın.

4. Yaklaşık 20 saniye bekleyin.

5. Ardından `port1` arayüzünü tekrar etkinleştirin.

6. **Log & Report > System Events** menüsüne gidin.

7. Ekranın sağ üst köşesindeki zaman aralığı listesinden **1 hour** seçeneğini belirleyin.

Böylece son bir saat içerisinde gerçekleşen sistem olaylarını görüntüleyebilirsiniz.

![System Events - 1 Hour](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-event-log-1h.png)

8. **Summary** sayfasında aşağı doğru ilerleyin.

9. **SD-WAN Events** özet bileşenine tıklayarak olay listesini genişletin.

![SD-WAN System Events](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-system-log-sd-wan.png)

10. Birkaç olay mesajına çift tıklayarak ayrıntılarını inceleyin.

### İlk "Service will be redirected in sequence order" mesajını inceleme

1. Alınan ilk **Service will be redirected in sequence order** mesajına çift tıklayın.

2. Açılan pencerede olayın ayrıntılarını inceleyin.

![First SD-WAN Event Details](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-system-log-message-details-initial.png)

`port1` devre dışı bırakıldıktan sonra FortiGate'in trafiği yalnızca **Member 2** üzerinden yönlendirdiğini görebilirsiniz.

Bu davranış, SD-WAN'ın kullanılabilir durumdaki diğer üye üzerinden trafik iletmeye devam ettiğini gösterir.

### İkinci "Service will be redirected in sequence order" mesajını inceleme

1. Alınan ikinci **Service will be redirected in sequence order** mesajına çift tıklayın.

2. Olayın ayrıntılarını inceleyin.

![Second SD-WAN Event Details](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/SDWAN/ex2-system-log-message-details.png)

Her iki arayüz de tekrar aktif olduğunda üye sıralaması yapılandırılmış sıraya geri döner:

1. Member 1 (`port1`)
2. Member 2 (`port2`)

Bu sayede FortiGate'in arayüz durumuna göre SD-WAN trafik yönlendirmesini dinamik olarak değiştirdiğini gözlemleyebilirsiniz.

## 5. Laboratuvarı Tamamlama

1. Local-FortiGate GUI oturumunu kapatın.
2. Local-Client VM üzerindeki terminal penceresini kapatın.

---

## Alıştırma Özeti

Bu alıştırmada aşağıdaki işlemleri gerçekleştirdiniz:

- Local-Client VM üzerinden internet trafiği oluşturdunuz.
- SD-WAN üyelerinin bant genişliği, trafik hacmi ve oturum dağılımlarını incelediniz.
- `Level3_DNS` Performance SLA üzerinden latency, packet loss ve jitter değerlerini gözlemlediniz.
- Forward Traffic logları üzerinden SD-WAN kurallarının trafik yönlendirmesini doğruladınız.
- `Critical-DIA` kuralının Dropbox trafiğini `port1` üzerinden yönlendirdiğini kontrol ettiniz.
- `Non-Critical-DIA` kuralının Facebook ve Pinterest trafiğini `port2` üzerinden yönlendirdiğini doğruladınız.
- Implicit SD-WAN rule davranışını öğrendiniz.
- `port1` devre dışı bırakıldığında trafiğin `port2` üzerinden devam ettiğini gözlemlediniz.
- Her iki arayüz yeniden aktif olduğunda SD-WAN üye sıralamasının yapılandırılan sıraya döndüğünü doğruladınız.

**Temel kazanım:** FortiGate SD-WAN, Performance SLA kontrolleri ve arayüz durumlarını kullanarak trafik yönlendirmesini dinamik biçimde yönetir. SD-WAN logları ve sistem olayları sayesinde bu davranışları izleyebilir ve doğrulayabilirsiniz.
