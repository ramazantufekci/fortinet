# LAB-4: FortiGate Üzerinde RADIUS Server Yapılandırması

Bu laboratuvarda FortiGate üzerinde uzak kullanıcı kimlik doğrulaması (Remote Authentication) için bir RADIUS Server yapılandıracaksınız.

Ardından RADIUS Server üzerindeki kullanıcı grubunu FortiGate üzerinde bir Firewall User Group içerisine ekleyecek ve bu grubu bir Firewall Policy kuralında kaynak (Source) olarak tanımlayacaksınız.

Son olarak uzak RADIUS kullanıcısıyla kimlik doğrulaması yapacak ve FortiGate yöneticisi olarak bu oturumu izleyeceksiniz.

---

## 1. FortiGate Üzerinde RADIUS Server Yapılandırma

Bu bölümde FortiGate'i, RADIUS Server olarak görev yapan ve önceden yapılandırılmış FortiAuthenticator sunucusuna bağlayacaksınız.

FortiAuthenticator, sunucu tabanlı parola doğrulaması (Server-Based Password Authentication) için kullanılacaktır.

### RADIUS Server Yapılandırması

1. Local-FortiGate GUI ekranına bağlanın.
2. Kullanıcı adı olarak `admin`, parola olarak `password` girerek oturum açın.
3. **User & Authentication > RADIUS Servers** menüsüne gidin.
4. **Create New** düğmesine tıklayın.
5. RADIUS Server için aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `RADIUS_Server` |
| Authentication method | `Default` |
| Primary Server IP/Name | `10.0.1.150` |
| Secret | `Training1!` |

**Ayarların açıklamaları:**

- **Name:** FortiGate üzerinde oluşturulacak RADIUS Server nesnesinin adıdır.
- **Authentication method:** Kimlik doğrulama yöntemi olarak `Default` kullanılır.
- **Primary Server IP/Name:** RADIUS Server olarak görev yapan FortiAuthenticator sunucusunun IP adresidir.
- **Secret:** FortiGate ile RADIUS Server arasında paylaşılan gizli anahtardır (Shared Secret). Her iki taraftaki değer birbiriyle eşleşmelidir.

6. **Test Connectivity** düğmesine tıklayın.

Bağlantı başarılıysa FortiGate, RADIUS Server ile iletişim kurabildiğini belirten bir mesaj gösterecektir.

7. **OK** düğmesine tıklayın.

Böylece FortiGate üzerinde `RADIUS_Server` isimli RADIUS Server tanımlanmış olur.

---

## 2. RADIUS User Group'u Firewall User Group'a Ekleme

Bu bölümde RADIUS Server üzerinde bulunan `Training` isimli kullanıcı grubunu, FortiGate üzerindeki `Training` isimli Firewall User Group içerisine ekleyeceksiniz.

RADIUS Server üzerindeki `Training` grubu aşağıdaki kullanıcıyı içermektedir:

- `radius1`

Kullanıcı grupları, ortak özelliklere veya erişim ihtiyaçlarına sahip kullanıcıları merkezi olarak yönetmek için kullanılır.

**Önemli:** `Training` isimli Firewall User Group laboratuvar ortamında önceden oluşturulmuştur. Yeni bir grup oluşturmanız gerekmiyor. Yalnızca uzak RADIUS Server üzerindeki kullanıcı grubunu mevcut gruba eklemeniz yeterlidir.

### Uzmanlık Görevi

Local-FortiGate (`10.0.1.254`) üzerinde RADIUS kullanıcı grubu olan `Training` grubunu, FortiGate Firewall User Group olan `Training` içerisine ekleyin.

### Kullanıcı Grubunu Ekleme

1. Local-FortiGate GUI üzerinde **User & Authentication > User Groups** menüsüne gidin.
2. Mevcut `Training` grubunu düzenlemek için üzerine tıklayın.

Grubun mevcut durumda bir Firewall Group olarak yapılandırıldığını göreceksiniz.

3. **Training** tablosunda **Add** düğmesine tıklayın.

**Add Group Match** penceresi açılacaktır.

4. **Remote Server** alanında `RADIUS_Server` sunucusunu seçin.
5. **Groups** alanında **Specify** seçeneğini işaretleyin.
6. Grup adı alanına `Training` yazın.
7. **OK** düğmesine tıklayın.

Artık RADIUS Server üzerindeki `Training` grubunda bulunan kullanıcı, FortiGate üzerindeki `Training` Firewall User Group içerisine dahil edilmiştir.

Yalnızca uzak RADIUS Server üzerinde bulunan ve bu grup eşleşmesine uyan kullanıcılar kimlik doğrulaması yapabilir.

**Önemli teknik bilgi:**

Uzak RADIUS Server, RADIUS Attribute-Value Pair (AVP) 26 olarak bilinen Vendor-Specific Attribute (VSA) özelliğini kullanacak şekilde yapılandırılmıştır.

Bu özellik sayesinde RADIUS yanıtına `Fortinet-Group-Name` isimli VSA eklenebilir.

FortiOS tarafında kullanıcı grubunun, RADIUS Server tarafından gönderilen bu grup adıyla eşleşecek şekilde yapılandırılması gerekir.

8. Grup düzenleme ekranında tekrar **OK** düğmesine tıklayarak değişiklikleri kaydedin.

---

## 3. Training User Group'u Firewall Policy'ye Ekleme

RADIUS Server üzerindeki kullanıcı grubu artık FortiGate'in `Training` Firewall User Group nesnesine eklenmiştir.

Şimdi bu grubu bir Firewall Policy kuralının kaynak (Source) alanına ekleyeceksiniz.

Böylece FortiGate, ağ kaynaklarına erişim kararlarını kullanıcı grubu üyeliğine göre verebilir.

### Training User Group'u Firewall Policy'ye Ekleme

1. Local-FortiGate GUI üzerinde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. Mevcut `port3` üzerinden `port1` arayüzüne trafik gönderen Firewall Policy kuralına çift tıklayın.
3. **Source** alanında `+` simgesine tıklayın.
4. **User** kategorisi altında bulunan `Training` grubunu seçin.
5. **OK** düğmesine tıklayın.

Böylece Firewall Policy, `Training` grubundaki kullanıcılar için kimlik doğrulaması isteyecektir.

---

## 4. RADIUS Kullanıcısının Kimlik Doğrulamasını Test Etme

Bu bölümde `radius1` kullanıcısının RADIUS Server üzerinden başarılı bir şekilde kimlik doğrulaması yapabildiğini CLI üzerinden test edeceksiniz.

### CLI Üzerinden RADIUS Authentication Testi

1. Local-FortiGate CLI ekranına bağlanın.
2. Kullanıcı adı olarak `admin`, parola olarak `password` girerek oturum açın.
3. Aşağıdaki komutu çalıştırın:

```bash
diagnose test authserver radius <RADIUS server name> mschap2 <RADIUS user name> <password>
```

Komuttaki parametreler aşağıdaki şekilde kullanılmalıdır:

| Parametre | Değer |
|---|---|
| RADIUS server name | `RADIUS_Server` |
| Authentication method | `mschap2` |
| RADIUS user name | `radius1` |
| Password | `Training!` |

Bu laboratuvar için çalıştırmanız gereken komut:

```bash
diagnose test authserver radius RADIUS_Server mschap2 radius1 Training!
```

Komut başarılı olduğunda RADIUS kimlik doğrulamasının başarılı olduğunu belirten bir mesaj görüntülenir.

4. Local-FortiGate CLI penceresini kapatın.

**Not:** RADIUS Server adı büyük/küçük harf duyarlıdır (Case Sensitive). Bu nedenle `RADIUS_Server` adını tam olarak bu şekilde yazmalısınız.

---

## 5. RADIUS Kullanıcısı ile Kimlik Doğrulama ve İzleme

Bu bölümde Firewall Policy üzerinden `radius1` kullanıcısıyla kimlik doğrulaması yapacaksınız.

Ardından FortiGate yöneticisi olarak bu kullanıcının aktif kimlik doğrulama oturumunu izleyeceksiniz.

### 5.1. Uzak RADIUS Kullanıcısı Olarak Oturum Açma

1. Local-Client sanal makinesine geçin.
2. Tarayıcıda yeni bir sekme açın.
3. Aşağıdaki web adresini ziyaret edin:

```text
http://elite-hackers.com
```

Ağ erişimi için kullanıcı adı ve parola isteyen bir kimlik doğrulama ekranı açılacaktır.

4. Aşağıdaki bilgilerle oturum açın:

| Alan | Değer |
|---|---|
| Username | `radius1` |
| Password | `Training!` |

Kimlik doğrulaması başarılı olduğunda kullanıcı ağa erişmeye çalışacaktır.

Ancak `elite-hackers.com` adresi, Firewall Policy üzerinde etkinleştirdiğiniz Web Filter Security Profile tarafından engellenmektedir.

Bu nedenle web sayfası açılmayacak ve engelleme mesajı görüntülenecektir.

Engellenen sayfada aşağıdaki gibi bilgiler yer alır:

- **URL:** Erişilmeye çalışılan web adresi.
- **Category:** Web Filter tarafından belirlenen kategori.

Bu sonuç, RADIUS kimlik doğrulamasının gerçekleştiğini ve Web Filter politikasının uygulandığını gösterir.

### 5.2. Aktif Kimliği Doğrulanmış Kullanıcıları İzleme

1. Local-FortiGate GUI üzerinde `admin` hesabıyla açık olan tarayıcı sekmesine geri dönün.
2. **Dashboard > Assets & Identities** menüsüne gidin.
3. **Firewall Users** bölümünü genişleterek tam ekran görünümüne geçin.

Bu ekranda FortiGate tarafından kimliği doğrulanmış aktif kullanıcıları görebilirsiniz.

Listede `radius1` kullanıcısı ve aşağıdaki gibi bilgiler görüntülenir:

- **User:** Kimliği doğrulanmış kullanıcı adı.
- **User Group:** Kullanıcının dahil olduğu Firewall User Group.
- **IP Address:** Kullanıcının ağ üzerindeki IP adresi.

4. Listeden `radius1` kullanıcısını seçin.
5. **Deauthenticate** düğmesine tıklayın.
6. Açılan **Confirm** penceresinde **OK** düğmesine tıklayarak işlemi onaylayın.

Kullanıcının kimlik doğrulama oturumu sonlandırılır.

Bunun sonucunda `radius1`, Firewall Users listesinden kaldırılır. Çünkü bu liste yalnızca aktif kimliği doğrulanmış kullanıcıları gösterir.

Kullanıcı, korunan ağ kaynaklarına tekrar erişebilmek için yeniden oturum açmalıdır.

**Önemli bilgi:**

`config user setting` CLI komutu, kullanıcıların ne kadar süreyle kimlik doğrulaması yapılmış durumda kalabileceğini belirleyen ayarları içerir.

Ancak yöneticiler, **Firewall User Monitor** listesinden bir kullanıcıyı seçip **Deauthenticate** düğmesine tıklayarak kullanıcının kimlik doğrulamasını manuel olarak da sonlandırabilir.

---

## 6. Firewall Policy Üzerinden User Group'u Kaldırma

Laboratuvarın son bölümünde, kimlik doğrulaması için Firewall Policy kuralına eklediğiniz `Training` grubunu kaldıracaksınız.

### Remote User Group'u Firewall Policy'den Kaldırma

1. Local-FortiGate GUI üzerinde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. Mevcut `port3` üzerinden `port1` arayüzüne trafik gönderen Firewall Policy kuralına çift tıklayın.
3. **Source** alanında bulunan `Training` kullanıcı grubunu kaldırın.
4. **Close** düğmesine tıklayın.
5. **OK** düğmesine tıklayarak değişiklikleri kaydedin.

Böylece Firewall Policy üzerindeki kullanıcı grubu tabanlı kimlik doğrulaması kaldırılmış olur.

---

## Laboratuvar Sonuçları

Bu laboratuvarı tamamladığınızda aşağıdaki FortiGate yapılandırmalarını öğrenmiş olacaksınız:

- FortiGate üzerinde RADIUS Server tanımlama.
- FortiAuthenticator sunucusunu RADIUS Server olarak kullanma.
- RADIUS bağlantısını **Test Connectivity** ile kontrol etme.
- RADIUS kullanıcı gruplarını FortiGate Firewall User Group içerisine ekleme.
- RADIUS grup eşleştirmesi (Group Match) yapılandırma.
- RADIUS Vendor-Specific Attribute (VSA) kavramını anlama.
- Remote User Group kullanarak Firewall Policy yapılandırma.
- CLI üzerinden RADIUS kullanıcı kimlik doğrulamasını test etme.
- Kimliği doğrulanmış aktif kullanıcıları Firewall Users ekranından izleme.
- Kullanıcıların kimlik doğrulama oturumlarını manuel olarak sonlandırma (Deauthenticate).

**Temel mantık:** RADIUS Server, kullanıcı kimlik doğrulamasını merkezi olarak gerçekleştirir. FortiGate, RADIUS Server üzerinden kullanıcının kimliğini doğrular ve kullanıcıyı grup eşleşmesine göre ilgili Firewall User Group içerisine dahil eder. Ardından Firewall Policy kuralları üzerinden kullanıcının ağ kaynaklarına erişimi kontrol edilir.
