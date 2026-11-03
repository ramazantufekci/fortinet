# LAB-3: Route Failover (Yedekli Rota) Yapılandırması

Bu laboratuvarda, Local-FortiGate üzerinde internete bağlı iki arayüz kullanılacaktır: `port1` ve `port2`.

Bu çalışmada `port1` birincil internet bağlantısı (Primary), `port2` ise yedek internet bağlantısı (Backup) olarak yapılandırılacaktır.

FortiGate, yalnızca `port1` bağlantısı devre dışı kaldığında `port2` bağlantısını kullanacaktır. Bunun için farklı Administrative Distance değerlerine sahip iki varsayılan rota (Default Route) oluşturulacak ve ardından birincil arayüz devre dışı bırakılarak yedek rotanın devreye girmesi sağlanacaktır.

---

## 1. Mevcut Routing Yapılandırmasını Kontrol Etme

Bu bölümde Local-FortiGate üzerindeki mevcut Routing yapılandırmasını inceleyeceksiniz.

### Uzmanlık Görevi

Local-FortiGate GUI (`admin/password`) üzerinden aşağıdaki işlemleri gerçekleştirin:

- Mevcut Static Route yapılandırmasını görüntüleyin.
- Static Routes sayfasında **Distance** ve **Priority** sütunlarını etkinleştirin.
- Mevcut Default Route için tanımlanmış **Distance** ve **Priority** değerlerini not edin.

Yardım almak veya yaptığınız işlemleri doğrulamak için aşağıdaki adımları izleyebilirsiniz.

### Routing Yapılandırmasını Kontrol Etme

1. Local-FortiGate GUI'ye bağlanın.
2. Kullanıcı adı olarak `admin`, parola olarak `password` girerek oturum açın.
3. **Network > Static Routes** menüsüne gidin.
4. `port1` arayüzü için tanımlanmış mevcut Default Route kaydını inceleyin.
5. Herhangi bir sütun başlığına sağ tıklayarak sütun ayarları menüsünü açın.
6. **Select Columns** bölümünde **Distance** ve **Priority** seçeneklerini işaretleyin.
7. **Apply** düğmesine tıklayın.

**Distance** ve **Priority** sütunları listeye eklenecektir.

Varsayılan olarak Static Route kayıtlarında:

- **Distance:** `10`
- **Priority:** `1`

değerleri kullanılır.

---

## 2. İkinci Default Route Oluşturma

Bu bölümde `port2` arayüzünü kullanarak ikinci bir Default Route oluşturacaksınız.

İkinci rotanın yedek bağlantı olarak kalmasını sağlamak için ilk rotadan daha yüksek bir Administrative Distance değeri atanacaktır.

### Uzmanlık Görevi

Local-FortiGate GUI üzerinden:

- `port2` arayüzünü kullanan ikinci bir Default Route oluşturun.
- **Distance** değerini `20` olarak ayarlayın.
- **Priority** değerini `5` olarak ayarlayın.

### İkinci Default Route Yapılandırması

1. Local-FortiGate GUI üzerinde **Network > Static Routes** menüsüne gidin.
2. **Create New** düğmesine tıklayın.
3. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Gateway Address | `10.200.2.254` |
| Interface | `port2` |
| Administrative Distance | `20` |

4. **Advanced Options** bölümünü genişletmek için `+` simgesine tıklayın.
5. **Priority** alanına `5` yazın.
6. **OK** düğmesine tıklayın.

FortiGate ikinci Default Route kaydını oluşturacaktır.

Böylece iki varsayılan rota aşağıdaki şekilde yapılandırılmış olur:

| Arayüz | Gateway | Distance | Priority | Rol |
|---|---|---:|---:|---|
| port1 | Mevcut gateway | 10 | 1 | Primary |
| port2 | 10.200.2.254 | 20 | 5 | Backup |

---

## 3. Firewall Policy Yapılandırması

Bu bölümde mevcut `Full_Access` Firewall Policy kuralını tüm oturumları kaydedecek şekilde düzenleyecek ve yedek internet bağlantısı için ikinci bir Firewall Policy oluşturacaksınız.

### Uzmanlık Görevi

Local-FortiGate GUI üzerinden:

- Mevcut `Full_Access` Firewall Policy kuralında tüm oturumlar için log kaydını etkinleştirin.
- `Backup_Access` adında ikinci bir Firewall Policy oluşturun.
- `Backup_Access` kuralını `port3` üzerinden gelen trafiği `port2` üzerinden internete gönderecek şekilde yapılandırın.
- NAT özelliğini etkinleştirin.
- Tüm oturumlar için log kaydını etkinleştirin.

### Firewall Policy Kurallarını Yapılandırma

1. Local-FortiGate GUI üzerinde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. Mevcut `Full_Access` kuralına çift tıklayarak düzenleme ekranını açın.
3. **Log Allowed Traffic** seçeneğini etkinleştirin.
4. Açılan seçeneklerden **All Sessions** değerini seçin.
5. **OK** düğmesine tıklayın.

**All Sessions** seçeneği, FortiGate'in yalnızca Security Profile tarafından incelenen oturumları değil, tüm trafik oturumlarını kaydetmesini sağlar. Böylece trafiğin hangi arayüz üzerinden yönlendirildiğini **Forward Traffic** log kayıtlarından doğrulayabilirsiniz.

### Backup_Access Firewall Policy Oluşturma

1. **Create New** düğmesine tıklayın.
2. İkinci Firewall Policy için aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `Backup_Access` |
| Incoming Interface | `port3` |
| Outgoing Interface | `port2` |
| Source | `LOCAL_SUBNET` |
| Destination | `all` |
| Schedule | `always` |
| Service | `ALL` |
| Action | `ACCEPT` |
| NAT | Enabled |
| Log Allowed Traffic | `All Sessions` |

3. **OK** düğmesine tıklayın.

Artık `port2` üzerinden internet erişimi sağlayacak ikinci bir Firewall Policy kuralınız bulunmaktadır.

---

## 4. Routing Table Görüntüleme

Local-FortiGate üzerinde artık farklı Distance değerlerine sahip iki Default Route bulunmaktadır.

Bu bölümde hangi rotanın aktif olarak Routing Table içerisine eklendiğini, hangi rotanın ise beklemede olduğunu inceleyeceksiniz.

### Routing Table Görüntüleme

1. Local-FortiGate CLI ekranına bağlanın.
2. Kullanıcı adı olarak `admin`, parola olarak `password` girerek oturum açın.
3. Aktif Routing Table kayıtlarını görüntülemek için aşağıdaki komutu çalıştırın:

```bash
get router info routing-table all
```

Çıktıda ikinci Default Route kaydının bulunmadığını göreceksiniz.

4. Routing Table Database kayıtlarını görüntülemek için aşağıdaki komutu çalıştırın:

```bash
get router info routing-table database
```

5. Çıktıyı inceleyerek ikinci Default Route kaydının **inactive** olarak işaretlendiğini doğrulayın.

**Önemli bilgi:**

- Yalnızca aktif rotaların başında `>` sembolü bulunur.
- `>` sembolü, ilgili rotanın seçildiğini ve aktif olarak kullanıldığını gösterir.
- Routing Table Database içerisinde FortiGate üzerindeki aktif, yedek ve pasif rotalar bulunur.

### Düşün ve Anla!

**port2 Default Route neden yedek rota olarak beklemektedir?**

Çünkü `port2` Default Route kaydının Administrative Distance değeri, `port1` rotasının değerinden daha yüksektir.

Aynı hedefe giden iki veya daha fazla rota farklı Distance değerlerine sahipse FortiGate, daha düşük Distance değerine sahip rotayı tercih eder.

Bu nedenle:

- `port1` rotasının Distance değeri `10` olduğu için aktif olarak kullanılır.
- `port2` rotasının Distance değeri `20` olduğu için Routing Table içerisine aktif rota olarak eklenmez.
- Ancak ikinci rota Routing Table Database içerisinde görüntülenebilir.

İlgili arayüz devre dışı kaldığında o arayüze bağlı rota da pasif duruma geçer.

6. Local-FortiGate CLI oturumunu kapatın.

---

## 5. Route Failover Testi

Bu bölümde öncelikle internet trafiğinin `port1` üzerinden geçtiğini doğrulayacaksınız.

Ardından `port1` arayüzünü devre dışı bırakarak Failover işlemini tetikleyecek ve internet trafiğinin otomatik olarak `port2` üzerinden yönlendirildiğini kontrol edeceksiniz.

### 5.1. port1 Rotasının Primary Olduğunu Doğrulama

1. Local-FortiGate GUI üzerinde **Log & Report > Forward Traffic** menüsüne gidin.
2. Herhangi bir sütun başlığına sağ tıklayarak sütun ayarları menüsünü açın.
3. **Select Columns** bölümünde **Destination Interface** seçeneğini işaretleyin.
4. Menüde aşağı doğru kaydırarak **Apply** düğmesine tıklayın.

**Destination Interface** sütunu log listesine eklenecektir.

5. Local-Client sanal makinesine geçin.
6. Tarayıcıda birkaç yeni sekme açarak aşağıdaki web sitelerini ziyaret edin:

- http://neverssl.com
- http://eu.httpbin.org

7. Local-FortiGate GUI ekranına geri dönün.
8. **Log & Report > Forward Traffic** menüsünü açın.
9. Yenileme simgesine tıklayın.
10. Ziyaret ettiğiniz web sitelerine ait log kayıtlarını bulun.
11. **Destination Interface** sütununda `port1` yazdığını doğrulayın.

Bu işlem, internet trafiğinin şu anda birincil rota olan `port1` üzerinden yönlendirildiğini doğrular.

### 5.2. Failover İşlemini Tetikleme

Şimdi birincil internet bağlantısını devre dışı bırakarak yedek rotanın devreye girmesini sağlayacaksınız.

1. Local-FortiGate GUI üzerinde **Network > Interfaces** menüsüne gidin.
2. `port1` arayüzüne çift tıklayarak düzenleme ekranını açın.
3. **Miscellaneous** bölümüne gidin.
4. Status ayarını **Disabled** olarak değiştirin.
5. **OK** düğmesine tıklayın.

Artık `port1` internet bağlantısı devre dışıdır.

FortiGate, ilgili arayüzü kullanamadığı için `port1` üzerinden tanımlanmış Default Route kaydını Routing Table içerisinden kaldırır.

### 5.3. Routing Table Değişikliğini Doğrulama

1. Local-FortiGate GUI üzerinde **Dashboard > Network** menüsüne gidin.
2. **Static & Dynamic Routing** bölümünü genişleterek tam ekran görünümüne geçin.
3. Routing Table kayıtlarını inceleyin.
4. `port1` rotasının yerini `port2` rotasının aldığını doğrulayın.

Bu aşamada FortiGate, yedek internet bağlantısı olan `port2` rotasını aktif olarak kullanmalıdır.

### 5.4. Traffic Log Kayıtlarını Doğrulama

1. Local-Client sanal makinesine geçin.
2. Tarayıcıda birkaç yeni sekme açarak aşağıdaki web sitelerini ziyaret edin:

- http://neverssl.com
- http://eu.httpbin.org

3. Local-FortiGate GUI ekranına geri dönün.
4. **Log & Report > Forward Traffic** menüsünü açın.
5. Ziyaret ettiğiniz web sitelerine ait log kayıtlarını bulun.
6. **Destination Interface** sütununu kontrol edin.
7. Değerin `port2` olduğunu doğrulayın.

Bu işlem, Local-FortiGate'in birincil bağlantı devre dışı kaldığında internet trafiğini otomatik olarak `port2` üzerinden yönlendirdiğini doğrular.

---

## 6. Routing Table Yapılandırmasını Eski Haline Getirme

Bir sonraki laboratuvara geçmeden önce `port1` arayüzünü yeniden etkinleştirmeniz gerekir.

Böylece birincil internet bağlantısı tekrar kullanılabilir hale gelecek ve `port1` rotası yeniden en iyi rota olarak seçilecektir.

### port1 Arayüzünü Yeniden Etkinleştirme

1. Local-FortiGate GUI üzerinde **Network > Interfaces** menüsüne gidin.
2. `port1` arayüzüne çift tıklayarak düzenleme ekranını açın.
3. **Miscellaneous** bölümüne gidin.
4. Status ayarını **Enabled** olarak değiştirin.
5. **OK** düğmesine tıklayın.

### Routing Table Yapılandırmasını Doğrulama

1. **Dashboard > Network** menüsüne gidin.
2. **Static & Dynamic Routing** bölümünü genişleterek tam ekran görünümüne geçin.
3. Routing Table kayıtlarını inceleyin.
4. `port2` rotasının yerini yeniden `port1` rotasının aldığını doğrulayın.

`port1` yeniden aktif hale geldiğinde Administrative Distance değeri daha düşük olduğu için birincil rota olarak seçilir.

5. Tarayıcıyı kapatın.

---

## Laboratuvar Sonuçları

Bu laboratuvarı tamamladığınızda aşağıdaki FortiGate yapılandırmalarını öğrenmiş olacaksınız:

- Birden fazla Default Route oluşturma.
- Administrative Distance ve Priority değerlerini yapılandırma.
- Primary ve Backup internet bağlantısı oluşturma.
- Farklı arayüzler üzerinden trafik yönlendirmek için Firewall Policy yapılandırma.
- Routing Table ve Routing Table Database arasındaki farkı anlama.
- CLI üzerinden Routing Table kayıtlarını görüntüleme.
- Forward Traffic log kayıtlarını kullanarak trafik yönünü doğrulama.
- Birincil bağlantı devre dışı kaldığında Route Failover işlemini test etme.
- Birincil bağlantı tekrar etkinleştirildiğinde otomatik olarak yeniden tercih edilmesini doğrulama.

**Temel mantık:** FortiGate, aynı hedefe giden rotalar arasında Administrative Distance değeri daha düşük olan rotayı tercih eder. Birincil rota kullanılamaz hale geldiğinde daha yüksek Distance değerine sahip yedek rota devreye girer.
