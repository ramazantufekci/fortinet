# LAB-5: FortiGate Üzerinde FSSO Kimlik Doğrulamasının Yapılandırılması

Bu laboratuvar çalışmasında FortiGate üzerinde FSSO (Fortinet Single Sign-On) yapılandırmasını gerçekleştirecek ve kullanıcı kimlik doğrulamasını test edeceksiniz.

Laboratuvar ortamında, aktif bir FSSO DC Agent'ın davranışını taklit etmek için Local-Client sanal makinesinde çalışan bir Python betiği kullanılmaktadır. Bu nedenle Local-Client VM üzerinden oturum açma olaylarını göndermek üzere ayrıca bir DC Agent yapılandırmanız gerekmeyecektir.

> **Bilgi:** Gerçek bir ortamda FortiGate'in kullanıcıları tanıyabilmesi için Active Directory oturum açma olaylarını bir FSSO Agent üzerinden izlemesi gerekir. Bunun için bir Collector Agent kurulmalı ve yapılandırılmalıdır. FSSO Agent yazılımlarına [Fortinet Support](https://support.fortinet.com) üzerinden ulaşabilirsiniz.

FortiGate'in FSSO Collector Agent ile iletişim kurabilmesi ve kullanıcı bilgilerini sorgulayabilmesi için öncelikle algılanan kullanıcıların bir güvenlik duvarı kullanıcı grubuna (Firewall User Group) atanması gerekir. Ardından bu kullanıcı grubu, bir güvenlik duvarı politikasının (Firewall Policy) kaynak (Source) bölümüne eklenmelidir.

Son olarak FortiGate tarafından alınan kullanıcı oturum açma olaylarını doğrulayabilirsiniz. Bu olay, kullanıcı Active Directory etki alanında oturum açtıktan sonra oluşturulur. Dolayısıyla bu işlem için ayrıca güvenlik duvarı üzerinden bir kimlik doğrulama işlemi yapılmasına gerek yoktur.

---

## 1. FortiGate Üzerindeki FSSO Yapılandırmasını İnceleme

Bu bölümde FortiGate üzerindeki FSSO sunucu yapılandırmasını ve FSSO kullanıcı gruplarını inceleyeceksiniz.

FSSO, FortiGate'in Single Sign-On (SSO) kullanarak ağa bağlanan kullanıcıları otomatik olarak tanımasını sağlar. Daha sonra FSSO kullanıcı gruplarını güvenlik duvarı politikalarına ekleyeceksiniz.

### 1.1. FortiGate Üzerindeki FSSO Sunucusunu ve Kullanıcı Grubunu İnceleme

1. Local-FortiGate GUI arayüzüne bağlanın.
2. Aşağıdaki bilgilerle oturum açın:
   - **Kullanıcı adı:** `admin`
   - **Parola:** `password`
3. **Security Fabric > External Connectors** menüsüne gidin.
4. **TrainingDomain** bağlantısını seçin ve **Edit** seçeneğine tıklayın.

![FortiGate FSSO Agent yapılandırması](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-Agent-7.4.png)

5. Sağ üst köşedeki **Endpoint/Identity** durumunu kontrol edin. Durumun **Disconnected** olduğunu göreceksiniz.
6. Tarayıcı penceresini açık bırakın.

### 1.2. Kullanıcı Oturum Açma Olayını Simüle Etme

1. Local-Client VM üzerinde bir terminal penceresi açın.
2. Aşağıdaki komutları çalıştırarak FSSO oturum açma olayını simüle edin:

```bash
cd Desktop/FSSO/

python2 fssoreplay.py -l 8000 -f sample.log
```

3. Terminal penceresini kapatmayın.

Python betiği arka planda çalışmaya devam edecektir.

### 1.3. FSSO Bağlantısını ve Kullanıcı Gruplarını İnceleme

1. Daha önce açık bıraktığınız **TrainingDomain** penceresine geri dönün.
2. **Apply & Refresh** butonuna tıklayın.
3. **TrainingDomain** bağlantısını seçin ve **Edit** seçeneğine tıklayın.
4. **Users/Groups** alanında bulunan **View** seçeneğine tıklayın.

![FortiGate FSSO kullanıcı ve grup bilgileri](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/Fabric-Identity-7.4.png)

İzlenen kullanıcı grubu olarak **TRAININGAD/AD-USERS** görüntülenecektir.

![FSSO kullanıcı grubu](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-Users.png)

5. **Collector Agent Group Filters** penceresini kapatmak için **X** simgesine tıklayın.
6. **OK** butonuna tıklayın.

FSSO Collector Agent ile iletişimin başarılı olduğunu gösteren yeşil yukarı yönlü ok simgesi görüntülenecektir.

![FortiGate FSSO bağlantı durumu](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-on-FGT-7.4.png)

### 1.4. FSSO Kullanıcısını Bir Kullanıcı Grubuna Atama

1. Local-FortiGate GUI üzerinde **User & Authentication > User Groups** menüsüne gidin.
2. **Create New** seçeneğine tıklayın.
3. Aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `Training` |
| Type | `Fortinet Single Sign-On (FSSO)` |
| Members | `TRAININGAD/AD-USERS` |

> **Bilgi:** Kullanıcı grubu türü olarak FSSO seçildiğinde, ilgili FSSO kullanıcı grubu otomatik olarak listelenir.

4. **OK** butonuna tıklayın.

---

## 2. FSSO Kullanıcılarını Güvenlik Duvarı Politikasına Ekleme

Bu bölümde FSSO kullanıcı grubunu bir güvenlik duvarı politikasının kaynak (Source) alanına ekleyeceksiniz.

Bu işlem sayesinde ağ kaynaklarına erişimi kullanıcı kimliğine göre kontrol edebilirsiniz.

### 2.1. FSSO Kullanıcı Grubunu Eklememeden Önce Bağlantıyı Test Etme

1. Local-Client VM üzerinde yeni bir tarayıcı penceresi açın.
2. [https://www.fortinet.com](https://www.fortinet.com) adresine gidin.

Bu aşamada tüm kullanıcıların Fortinet web sitesine erişebildiğini göreceksiniz.

### 2.2. FSSO Kullanıcı Grubunu Güvenlik Duvarı Politikasına Ekleme

1. Local-FortiGate GUI üzerinde oturum açtığınız tarayıcıya geri dönün.
2. **Policy & Objects > Firewall Policy** menüsüne gidin.
3. **Full_Access** isimli güvenlik duvarı politikasını düzenleyin.
4. **Source** alanında bulunan `LOCAL_SUBNET` nesnesine tıklayın.
5. **Select Entries** bölümünde:
   - **User** seçeneğini belirleyin.
   - `Training` grubunu seçerek kaynak listesine ekleyin.

![FSSO kullanıcı grubunun firewall politikasına eklenmesi](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-Policy-7.4.png)

6. **Close** butonuna tıklayın.
7. Ardından **OK** butonuna tıklayarak politikayı kaydedin.

![FSSO firewall policy durumu](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-Policy-Status-7.4.png)

---

## 3. FSSO Yapılandırmasını Test Etme

Bir kullanıcı oturum açtıktan sonra FortiGate, kullanıcının IP adresi üzerinden kimliğini otomatik olarak belirler.

Böylece güvenlik duvarı politikaları kullanıcı kimliğine göre değerlendirilir ve kullanıcının ağ kaynaklarına erişimine izin verilir veya erişimi engellenir.

### 3.1. FSSO Kullanıcı Grubu Eklendikten Sonra Bağlantıyı Test Etme

1. Local-Client VM üzerinde yeni bir tarayıcı sekmesi açın.
2. [http://support.fortinet.com](http://support.fortinet.com) adresine gidin.

> **Bilgi:** Local-Client VM üzerinde çalışan Python betiği, aşağıdaki kullanıcı oturum açma bilgilerini FortiGate'e zaten göndermektedir:
>
> - **Kullanıcı:** `aduser1`
> - **IP adresi:** `10.0.1.10`

Bu örnekte `aduser1` kullanıcısı, güvenlik duvarı politikasında tanımlanan kullanıcı grubuna üye olduğu için web sitesine başarıyla erişebilir.

### 3.2. FSSO Collector Agent ile FortiGate Arasındaki Bağlantı Durumunu İnceleme

1. Local-FortiGate CLI arayüzüne bağlanın.
2. Aşağıdaki bilgilerle oturum açın:
   - **Kullanıcı adı:** `admin`
   - **Parola:** `password`
3. Collector Agent ile FortiGate arasındaki bağlantı durumunu görüntülemek için aşağıdaki komutları çalıştırın:

```shell
diagnose debug enable

diagnose debug authd fsso server-status
```

4. CLI çıktısını inceleyin.

FortiGate'in FSSO Collector Agent ile bağlantı kurduğunu göreceksiniz.

Örnek çıktı:

```text
Server Name       Connection Status    Version          Address
-----------       -----------------    -------          -------
TrainingDomain    connected            FSAE server 1.1  10.0.1.10
```

Bu çıktı, `TrainingDomain` isimli FSSO sunucusunun bağlı olduğunu göstermektedir.

### 3.3. FSSO Collector Agent ile FortiGate Arasındaki İletişimi İzleme

1. Local-FortiGate CLI üzerinde aşağıdaki komutları çalıştırın:

```shell
diagnose debug enable

diagnose debug application authd 8256
```

2. Local-Client VM üzerindeki terminal penceresine geçin.
3. Çalışan Python betiğini durdurmak için `Ctrl + C` tuşlarına basın.
4. Kullanıcı oturum açma olayını yeniden simüle etmek için aşağıdaki komutu tekrar çalıştırın:

```bash
python2 fssoreplay.py -l 8000 -f sample.log
```

5. FortiGate CLI ekranındaki debug çıktısını inceleyin.

Örnek çıktı:

```text
_process_logon: 1079]: ADUSER1(10.0.1.10, 0) logged on from TrainingDomain.

_process_logon:1122di]: ADUSER1 (10.0.1.10, 0) from TrainingDomain exists

fsae_io_ctx_process_msg[TrainingDomain]: received heartbeat 100004

fsae_io_ctx_process_msg[TrainingDomain]: received heartbeat 100005
```

> **Bilgi:** Local-Client VM üzerinde bir oturum açma olayı oluşturdunuz ve bu olay FortiGate'e iletildi.

6. Debug işlemini durdurmak için aşağıdaki komutu çalıştırın:

```shell
diagnose debug reset
```

### 3.4. FSSO Oturum Açma Olaylarını Görüntüleme

1. Local-FortiGate CLI üzerinde aşağıdaki komutu çalıştırın:

```shell
diagnose debug authd fsso list
```

2. Komut çıktısında listelenen FSSO oturum açma olaylarını inceleyin.

Örnek çıktı:

```text
----FSSO logons----

IP:10.0.1.10 User: ADUSER1 Groups: TRAINING/AD-USERS Workstation

C7280677811.TRAININGAD.TRAINING.LAB MemberOf: Training TRAININGAD/AD-USERS

Total number of logons listed: 1, filtered: 0

----end of FSSO logons----
```

Bu çıktı aşağıdaki bilgileri doğrulamaktadır:

- **IP adresi:** `10.0.1.10`
- **Kullanıcı:** `ADUSER1`
- **Grup:** `TRAINING/AD-USERS`
- **Üye olduğu FortiGate grubu:** `Training`

FortiGate, ilgili kullanıcının oturum açma bilgisini başarıyla almış ve kullanıcıyı tanımlanan FSSO grubuyla ilişkilendirmiştir.

### 3.5. Kullanıcı Olay Günlüklerini İnceleme

1. Local-FortiGate GUI arayüzüne bağlanın.
2. `admin` kullanıcı adı ve `password` parolasıyla oturum açın.
3. **Log & Report > System Events** menüsüne gidin.
4. **User Events** bileşeninde bulunan **View Logs** ok simgesine tıklayın.

![FortiGate User Events ekranı](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-UserEvents-widget-7.4.png)

5. Listeden bir günlük kaydı seçin.
6. Daha fazla bilgi görüntülemek için **Details** butonuna tıklayın.

![FortiGate FSSO kullanıcı olay günlükleri](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-UserEvents-7.4.png)

### 3.6. FSSO Oturum Açma Olaylarını İzleme

1. Local-FortiGate GUI üzerinde **Dashboard > Assets & Identities** menüsüne gidin.
2. **Firewall Users** bileşenini tam ekran açmak için üzerine çift tıklayın.
3. **Show all FSSO Logons** seçeneğine tıklayın.
4. Kullanıcı bilgileri görüntülenmiyorsa **Refresh** butonuna tıklayın.

![FortiGate FSSO kullanıcı izleme ekranı](https://flarexhtmlbucket.s3.us-west-2.amazonaws.com/operations/FortiGate_7.4_Administrator/FortiGate_Administrator/Lab_Guide/Images/FSSO/FSSO-Monitor-7.4.png)

---

## 4. Laboratuvar Çalışmasının Sonucu

Bu laboratuvar çalışmasında FortiGate üzerinde FSSO kimlik doğrulamasının nasıl yapılandırıldığını ve test edildiğini öğrendiniz.

Gerçekleştirilen işlemler:

- FortiGate üzerindeki FSSO Collector Agent bağlantısı incelendi.
- Python betiği kullanılarak kullanıcı oturum açma olayı simüle edildi.
- FSSO kullanıcı grubu görüntülendi.
- `Training` isimli FSSO kullanıcı grubu oluşturuldu.
- FSSO kullanıcı grubu `Full_Access` güvenlik duvarı politikasına eklendi.
- Kullanıcının IP adresi üzerinden otomatik olarak tanınması test edildi.
- FortiGate CLI üzerinden FSSO bağlantı durumu ve kullanıcı oturum açma kayıtları incelendi.
- GUI üzerinden kullanıcı olay günlükleri ve FSSO oturumları görüntülendi.

**Sonuç:** FortiGate, FSSO Collector Agent üzerinden gelen kullanıcı oturum açma olayını başarıyla aldı. `10.0.1.10` IP adresine sahip `ADUSER1` kullanıcısını tanıdı ve bu kullanıcıyı `Training` FSSO grubuyla ilişkilendirdi. Böylece güvenlik duvarı politikalarının kullanıcı kimliğine göre uygulanabildiği doğrulandı.
