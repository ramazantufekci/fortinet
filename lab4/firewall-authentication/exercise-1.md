# LAB-4: LDAP Server Yapılandırması

Bu laboratuvarda FortiGate üzerinde uzak kullanıcı kimlik doğrulaması (Remote Authentication) için bir LDAP Server yapılandıracaksınız.

Ardından LDAP sunucusundaki kullanıcı grubunu FortiGate üzerinde bir Firewall User Group içerisine ekleyecek ve bu grubu bir Firewall Policy kuralında kaynak (Source) olarak tanımlayacaksınız.

Son olarak uzak LDAP kullanıcısıyla kimlik doğrulaması yapacak ve FortiGate yöneticisi olarak bu oturumu izleyeceksiniz.

---

## 1. FortiGate Üzerinde LDAP Server Yapılandırma

Bu bölümde FortiGate'i, LDAP Server olarak görev yapan ve önceden yapılandırılmış FortiAuthenticator sunucusuna bağlayacaksınız.

FortiAuthenticator, sunucu tabanlı parola doğrulaması (Server-Based Password Authentication) için kullanılacaktır.

### LDAP Server Yapılandırması

1. Local-FortiGate GUI ekranına bağlanın.
2. Kullanıcı adı olarak `admin`, parola olarak `password` girerek oturum açın.
3. **User & Authentication > LDAP Servers** menüsüne gidin.
4. **Create New** düğmesine tıklayın.
5. LDAP Server için aşağıdaki ayarları yapılandırın:

| Alan | Değer |
|---|---|
| Name | `External_Server` |
| Server IP/Name | `10.0.1.150` |
| Server Port | `389` |
| Common Name Identifier | `uid` |
| Distinguished Name | `ou=Training,dc=trainingAD,dc=training,dc=lab` |
| Bind Type | `Regular` |
| Username | `uid=adadmin,cn=Users,dc=trainingAD,dc=training,dc=lab` |
| Password | `Training!` |

**Ayarların açıklamaları:**

- **Server IP/Name:** LDAP Server olarak görev yapan FortiAuthenticator sunucusunun IP adresidir.
- **Server Port:** LDAP protokolünün varsayılan portu olan `389` kullanılır.
- **Common Name Identifier:** LDAP dizininde kullanıcı adını bulmak için kullanılan özniteliktir. Bu laboratuvarda `uid` kullanılır.
- **Distinguished Name:** LDAP dizininin konumunu belirtir. Bu yapılandırmada tüm kullanıcılar `Training` Organizational Unit (OU) altında bulunur.
- **Bind Type:** `Regular` seçilerek LDAP bağlantısında kullanıcı adı ve parola ile kimlik doğrulaması yapılır.
- **Username:** LDAP Server'a bağlanmak için kullanılacak `adadmin` hesabının Distinguished Name bilgisidir.
- **Password:** `adadmin` kullanıcısı için önceden tanımlanmış paroladır.

6. **Test Connectivity** düğmesine tıklayın.

Bağlantı başarılıysa FortiGate, LDAP Server ile iletişim kurabildiğini belirten bir mesaj gösterecektir.

7. **OK** düğmesine tıklayın.

Böylece FortiGate üzerinde `External_Server` isimli LDAP Server tanımlanmış olur.

---

## 2. LDAP User Group'u Firewall User Group'a Ekleme

Bu bölümde LDAP Server üzerinde bulunan `AD_users` isimli kullanıcı grubunu FortiGate üzerindeki `Remote-users` isimli Firewall User Group içerisine ekleyeceksiniz.

LDAP üzerindeki `AD_users` grubu aşağıdaki iki kullanıcıyı içermektedir:

- `aduser1`
- `aduser2`

Kullanıcı grupları, ortak özelliklere veya erişim ihtiyaçlarına sahip kullanıcıları merkezi olarak yönetmek için kullanılır.

**Önemli:** `Remote-users` isimli Firewall User Group laboratuvar ortamında önceden oluşturulmuştur. Bu nedenle yeni bir grup oluşturmanız gerekmiyor. Yalnızca LDAP Server üzerindeki kullanıcı grubunu mevcut gruba eklemeniz yeterlidir.

### Uzmanlık Görevi

Local-FortiGate (`10.0.1.254`) üzerinde Active Directory kullanıcı grubu olan `AD_users` grubunu, FortiGate Firewall User Group olan `Remote-users` içerisine ekleyin.

### Kullanıcı Grubunu Ekleme

1. Local-FortiGate GUI üzerinde **User & Authentication > User Groups** menüsüne gidin.
2. Mevcut `Remote-users` grubunu düzenlemek için üzerine tıklayın.

Grubun mevcut durumda bir Firewall Group olarak yapılandırıldığını göreceksiniz.

3. **Remote Groups** tablosunda **Add** düğmesine tıklayın.

**Add Group Match** penceresi açılacaktır.

4. **Remote Server** alanında `External_Server` sunucusunu seçin.
5. **Groups** sekmesine geçin.
6. `AD_users` grubuna sağ tıklayın.
7. **Add Selected** seçeneğine tıklayın.

`AD_users` grubunun yanında yeşil onay işareti görünecektir. Bu işaret grubun eklendiğini gösterir.

8. **OK** düğmesine tıklayın.
9. Ardından `Remote-users` grup düzenleme ekranında tekrar **OK** düğmesine tıklayarak değişiklikleri kaydedin.

Artık Active Directory üzerindeki `AD_users` grubuna dahil olan kullanıcılar FortiGate üzerindeki `Remote-users` Firewall User Group içerisinde yer alır.

Yalnızca LDAP Server üzerinde tanımlı olan ve bu grup eşleşmesine uyan kullanıcılar kimlik doğrulaması yapabilir.

---

## 3. Remote User Group'u Firewall Policy'ye Ekleme

LDAP Server, `Remote-users` Firewall User Group içerisine başarıyla eklendi.

Şimdi bu kullanıcı grubunu bir Firewall Policy kuralının kaynak (Source) alanına ekleyeceksiniz.

Böylece FortiGate, ağ kaynaklarına erişim kararlarını kullanıcı grubu üyeliğine göre verebilir.

### Remote User Group'u Firewall Policy'ye Ekleme

1. Local-FortiGate GUI üzerinde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. Mevcut `port3` üzerinden `port1` arayüzüne trafik gönderen Firewall Policy kuralına çift tıklayın.
3. **Source** alanında `+` simgesine tıklayın.
4. **User** kategorisi altında bulunan `Remote-users` grubunu seçin.

Böylece Firewall Policy kuralı, `Remote-users` grubuna dahil olan kullanıcılar için kimlik doğrulaması isteyecektir.

### Web Filter Yapılandırması

5. **Security Profiles** bölümüne gidin.
6. **Web Filter** seçeneğini etkinleştirin.
7. Web Filter profili olarak `Category_Monitor` seçin.

Bu Web Filter profili laboratuvar ortamında önceden yapılandırılmıştır.

`Category_Monitor` aşağıdaki kategorileri engelleyecek şekilde ayarlanmıştır:

- **Potentially Liable**
- **Adult/Mature Content**
- **Security Risk**

### Logging Yapılandırması

8. **Logging Options** bölümüne gidin.
9. **Log Allowed Traffic** seçeneğinin etkin olduğundan emin olun.
10. Log türü olarak **All Sessions** seçin.
11. **OK** düğmesine tıklayın.

Böylece Firewall Policy, `Remote-users` grubundaki kullanıcıların kimlik doğrulamasını yapacak ve tanımlanan Web Filter kurallarını uygulayacaktır.

---

## 4. LDAP Kullanıcısının Kimlik Doğrulamasını Test Etme

Bu bölümde `aduser1` kullanıcısının LDAP Server üzerinden başarılı bir şekilde kimlik doğrulaması yapabildiğini CLI üzerinden test edeceksiniz.

### CLI Üzerinden LDAP Authentication Testi

1. Local-FortiGate CLI ekranına bağlanın.
2. Kullanıcı adı olarak `admin`, parola olarak `password` girerek oturum açın.
3. Aşağıdaki komutu çalıştırın:

```bash
diagnose test authserver ldap <LDAP server name> <LDAP user name> <password>
```

Komuttaki parametreler aşağıdaki şekilde kullanılmalıdır:

| Parametre | Değer |
|---|---|
| LDAP server name | `External_Server` |
| LDAP user name | `aduser1` |
| Password | `Training!` |

Bu laboratuvar için çalıştırmanız gereken komut:

```bash
diagnose test authserver ldap External_Server aduser1 Training!
```

Komut başarılı olduğunda LDAP kimlik doğrulamasının başarılı olduğunu belirten bir mesaj görüntülenir.

4. Local-FortiGate CLI penceresini kapatın.

**Not:** LDAP Server adı büyük/küçük harf duyarlıdır (Case Sensitive). Bu nedenle `External_Server` adını tam olarak bu şekilde yazmalısınız.

---

## 5. Uzak Kullanıcı ile Kimlik Doğrulama ve İzleme

Bu bölümde Firewall Policy üzerinden `aduser1` kullanıcısıyla kimlik doğrulaması yapacaksınız.

Ardından FortiGate yöneticisi olarak bu kullanıcının aktif kimlik doğrulama oturumunu izleyeceksiniz.

### 5.1. Uzak Kullanıcı Olarak Oturum Açma

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
| Username | `aduser1` |
| Password | `Training!` |

Kimlik doğrulaması başarılı olduğunda kullanıcı ağa erişmeye çalışacaktır.

Ancak `elite-hackers.com` adresi, Firewall Policy üzerinde etkinleştirdiğiniz Web Filter Security Profile tarafından engellenmektedir.

Bu nedenle web sayfası açılmayacak ve engelleme mesajı görüntülenecektir.

Engellenen sayfada aşağıdaki gibi bilgiler yer alır:

- **URL:** Erişilmeye çalışılan web adresi.
- **Category:** Web Filter tarafından belirlenen kategori.

Bu sonuç, LDAP kimlik doğrulamasının gerçekleştiğini ve Web Filter politikasının uygulandığını gösterir.

### 5.2. Aktif Kimliği Doğrulanmış Kullanıcıları İzleme

1. Local-FortiGate GUI üzerinde `admin` hesabıyla açık olan tarayıcı sekmesine geri dönün.
2. **Dashboard > Assets & Identities** menüsüne gidin.
3. **Firewall Users** bölümünü genişleterek tam ekran görünümüne geçin.

Bu ekranda FortiGate tarafından kimliği doğrulanmış aktif kullanıcıları görebilirsiniz.

Listede `aduser1` kullanıcısı ve aşağıdaki gibi bilgiler görüntülenir:

- **User:** Kimliği doğrulanmış kullanıcı adı.
- **User Group:** Kullanıcının dahil olduğu Firewall User Group.
- **IP Address:** Kullanıcının ağ üzerindeki IP adresi.

4. Listeden `aduser1` kullanıcısına tıklayın.
5. **Deauthenticate** düğmesine tıklayın.
6. Açılan **Confirm** penceresinde **OK** düğmesine tıklayarak işlemi onaylayın.

Kullanıcının kimlik doğrulama oturumu sonlandırılır.

Bunun sonucunda `aduser1`, Firewall Users listesinden kaldırılır. Çünkü bu liste yalnızca aktif kimliği doğrulanmış kullanıcıları gösterir.

Kullanıcı, korunan ağ kaynaklarına tekrar erişebilmek için yeniden oturum açmalıdır.

**Önemli bilgi:**

`config user setting` CLI komutu, kullanıcıların ne kadar süreyle kimlik doğrulaması yapılmış durumda kalabileceğini belirleyen ayarları içerir.

Ancak yöneticiler, **Firewall User Monitor** listesinden bir kullanıcıyı seçip **Deauthenticate** düğmesine tıklayarak kullanıcının kimlik doğrulamasını manuel olarak da sonlandırabilir.

---

## 6. Firewall Policy Üzerinden User Group'u Kaldırma

Laboratuvarın son bölümünde, kimlik doğrulaması için Firewall Policy kuralına eklediğiniz `Remote-users` grubunu kaldıracaksınız.

### Remote User Group'u Firewall Policy'den Kaldırma

1. Local-FortiGate GUI üzerinde **Policy & Objects > Firewall Policy** menüsüne gidin.
2. Mevcut `port3` üzerinden `port1` arayüzüne trafik gönderen Firewall Policy kuralına çift tıklayın.
3. **Source** alanında bulunan `Remote-users` kullanıcı grubunu kaldırın.
4. **Close** düğmesine tıklayın.
5. **OK** düğmesine tıklayarak değişiklikleri kaydedin.

Böylece Firewall Policy üzerindeki kullanıcı grubu tabanlı kimlik doğrulaması kaldırılmış olur.

---

## Laboratuvar Sonuçları

Bu laboratuvarı tamamladığınızda aşağıdaki FortiGate yapılandırmalarını öğrenmiş olacaksınız:

- FortiGate üzerinde LDAP Server tanımlama.
- FortiAuthenticator sunucusunu LDAP Server olarak kullanma.
- LDAP bağlantısını **Test Connectivity** ile kontrol etme.
- LDAP kullanıcı gruplarını FortiGate Firewall User Group içerisine ekleme.
- Remote User Group kullanarak Firewall Policy yapılandırma.
- Firewall Policy üzerinde kullanıcı grubu tabanlı kimlik doğrulaması uygulama.
- Web Filter Security Profile kullanarak web erişimini kontrol etme.
- CLI üzerinden LDAP kullanıcı kimlik doğrulamasını test etme.
- Kimliği doğrulanmış aktif kullanıcıları Firewall Users ekranından izleme.
- Kullanıcıların kimlik doğrulama oturumlarını manuel olarak sonlandırma (Deauthenticate).

**Temel mantık:** LDAP Server, kullanıcı bilgilerini ve grup üyeliklerini merkezi olarak yönetir. FortiGate ise LDAP üzerinden kullanıcı kimliğini doğrular ve kullanıcıyı eşleştiği Firewall User Group üzerinden ilgili Firewall Policy kurallarına tabi tutar.
