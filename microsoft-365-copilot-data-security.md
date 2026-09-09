# Microsoft 365 Copilot Data Security

> **Yazar:** Emre ERBULMUŞ
> **İlk yayın:** 25 Aralık 2025 · **Güncelleme:** Eylül 2026
> **Orijinal makale:** <https://www.emreerbulmus.com/microsoft-365-copilot-data-security/>

Bu doküman, Microsoft 365 Copilot dağıtımı öncesinde ve sonrasında alınması gereken veri güvenliği aksiyonlarını uçtan uca ele alır. 2026 itibarıyla Microsoft tarafında değişen/yeni gelen kabiliyetler (Copilot Control System, DSPM for AI, Restricted Content Discovery, ajan yönetişimi) eklenmiş, her başlık için uygulanabilir bir **kontrol listesi** hazırlanmıştır.

---

## İçindekiler

- [1. Giriş](#1-giriş)
- [2. Copilot Nasıl Çalışır? "On Behalf Of" Prensibi](#2-copilot-nasıl-çalışır-on-behalf-of-prensibi)
- [3. Yeni Nesil Çerçeve: Copilot Control System (CCS)](#3-yeni-nesil-çerçeve-copilot-control-system-ccs)
- [4. Dağıtım Öncesi Yapılandırma Adımları](#4-dağıtım-öncesi-yapılandırma-adımları)
  - [4.1 Lisans ve erişim yönetimi](#41-lisans-ve-erişim-yönetimi)
  - [4.2 Paylaşım (sharing) ayarlarının sıkılaştırılması](#42-paylaşım-sharing-ayarlarının-sıkılaştırılması)
  - [4.3 Content Management Assessment ve Data Access Governance](#43-content-management-assessment-ve-data-access-governance)
  - [4.4 Restricted Content Discovery (RCD)](#44-restricted-content-discovery-rcd)
  - [4.5 Restricted Access Control (RAC)](#45-restricted-access-control-rac)
  - [4.6 Site yaşam döngüsü, arşivleme ve atıl içerik temizliği](#46-site-yaşam-döngüsü-arşivleme-ve-atıl-içerik-temizliği)
  - [4.7 Search / indexing kontrolü](#47-search--indexing-kontrolü)
  - [4.8 Microsoft 365 Groups yönetimi](#48-microsoft-365-groups-yönetimi)
  - [4.9 Microsoft Teams External Access kontrolü](#49-microsoft-teams-external-access-kontrolü)
- [5. Microsoft Purview ile Veri Güvenliği](#5-microsoft-purview-ile-veri-güvenliği)
  - [5.1 DSPM for AI — AI güvenliğinin ön kapısı](#51-dspm-for-ai--ai-güvenliğinin-ön-kapısı)
  - [5.2 Sensitivity Label ve şifreleme](#52-sensitivity-label-ve-şifreleme)
  - [5.3 DLP for Microsoft 365 Copilot](#53-dlp-for-microsoft-365-copilot)
  - [5.4 Insider Risk Management ve Adaptive Protection](#54-insider-risk-management-ve-adaptive-protection)
  - [5.5 Audit (denetim kayıtları)](#55-audit-denetim-kayıtları)
  - [5.6 Retention / Data Lifecycle Management](#56-retention--data-lifecycle-management)
  - [5.7 Communication Compliance](#57-communication-compliance)
  - [5.8 eDiscovery — Copilot verisini arama ve silme](#58-ediscovery--copilot-verisini-arama-ve-silme)
  - [5.9 Compliance Manager](#59-compliance-manager)
- [6. Defender for Cloud Apps ile Public/External Dosya Yönetimi](#6-defender-for-cloud-apps-ile-publicexternal-dosya-yönetimi)
- [7. Ajan (Agent) Yönetişimi — 2026'nın Yeni Gündemi](#7-ajan-agent-yönetişimi--2026nın-yeni-gündemi)
- [8. Yedekleme ve Kurtarma](#8-yedekleme-ve-kurtarma)
- [9. Kontrol Listesi (Checklist)](#9-kontrol-listesi-checklist)
- [10. Faydalı PowerShell Komutları](#10-faydalı-powershell-komutları)
- [11. Kaynaklar](#11-kaynaklar)

---

## 1. Giriş

Yoğun bir iş gününde gelen kutusunda biriken e-postalar, arka arkaya toplantılar, nerede olduğu hatırlanmayan dosyalar ve Teams sohbetleri arasında kaybolan bilgi… Microsoft 365 Copilot tam da bu noktada devreye girer. Word, Excel, PowerPoint, Outlook ve Teams gibi günlük uygulamalara entegre çalışır; kısa komutlarla bilgiye ulaşmayı, uzun içerikleri özetlemeyi ve veriyi hızlıca elde etmeyi sağlar.

Copilot'un en kritik özelliği ise tüm bunları **kurumun mevcut güvenlik, izin ve uyumluluk politikalarına bağlı kalarak** yapmasıdır. Ancak bu, "kurulunca güvenli" anlamına gelmez: Copilot mevcut izin hijyeninizin aynasıdır. Ortamınızda aşırı paylaşım (oversharing) varsa, Copilot bunu görünür ve erişilebilir hâle getirir.

Bu nedenle Copilot dağıtımı bir **lisans projesi değil, bir veri yönetişimi projesidir.**

---

## 2. Copilot Nasıl Çalışır? "On Behalf Of" Prensibi

Copilot; büyük dil modelleri (LLM), Microsoft Graph ve Microsoft 365 uygulamalarını bir araya getirerek çalışır. İşleyişte mevcut erişim kontrollerine ve uyumluluk politikalarına sıkı sıkıya bağlı kalınır.

Temel nokta, Copilot'un veriler üzerinde **"on behalf of"** yani **kullanıcı adına** çalışmasıdır:

- Kendi posta kutunuzdan *"Bana geçen hafta Caner'den gelen e-postaların özetini çıkar"* dediğinizde Copilot yanıt üretir.
- CEO'nun posta kutusundan veri talep ettiğinizde ise erişim yetkiniz olmadığı için **yanıt üretemez.**

Copilot'un bağımsız/genel bir erişim yetkisi yoktur; yalnızca kullanıcının yetkisi kapsamında hizmet verir. Buna ek olarak:

- **Veri Microsoft 365 hizmet sınırının (service boundary) dışına çıkmaz.**
- **Kurumsal veriler temel LLM'lerin eğitiminde kullanılmaz.**
- Sensitivity label ile şifrelenmiş içerikte Copilot, kullanıcının **VIEW + EXTRACT** kullanım haklarını doğrular; EXTRACT hakkı yoksa içerik yanıtta kullanılmaz.
- **S/MIME** ile korunan e-postalar Copilot tarafından döndürülmez; **parola korumalı** belgelere ise yalnızca kullanıcı belgeyi zaten açmışsa erişilebilir.

---

## 3. Yeni Nesil Çerçeve: Copilot Control System (CCS)

2026 itibarıyla Microsoft, Copilot ve ajanlara yönelik güvenlik/yönetişim kontrollerini **Copilot Control System (CCS)** çatısı altında topladı. CCS üç sütundan oluşur:

| Sütun | Kapsam |
|---|---|
| **Security & Governance** | Veri güvenliği, AI güvenliği, uyumluluk ve gizlilik |
| **Management Controls** | Lisans, erişim, ajan politikaları, dağıtım |
| **Measurement & Reporting** | Benimseme, kullanım ve etki raporlaması |

Microsoft kontrolleri lisans seviyesine göre iki grupta tanımlar:

- **Foundational (A3/E3/G3):** Microsoft 365 admin center, SharePoint Advanced Management ve temel Purview kontrolleri.
- **Optimized (A5/E5/G5):** Purview'ın ileri kabiliyetleri (DSPM for AI risk değerlendirmeleri, DLP for Copilot, Insider Risk, Communication Compliance, Adaptive Protection) ve Defender for Cloud Apps.

> **Not:** Bu makaledeki adımlar CCS'in *Security & Governance* sütununu pratik aksiyonlara dönüştürür.

---

## 4. Dağıtım Öncesi Yapılandırma Adımları

### 4.1 Lisans ve erişim yönetimi

Copilot lisansları atandıktan sonra deneyim, Microsoft 365 uygulamalarında kullanıcılar için otomatik olarak görünür hâle gelir (örneğin Word'de yeni belge oluştururken açılan Copilot iletişim kutusu).

**Öneri:** Lisansı tüm kuruma tek seferde dağıtmak yerine **pilot → dalga bazlı (wave) dağıtım** yapın. İlk dalga öncesinde bu makaledeki 4.2–4.6 arası adımların tamamlanmış olması gerekir.

> Copilot Control System içindeki agent/access politikaları ile hangi kullanıcı gruplarının hangi Copilot ve ajan deneyimlerine erişeceğini tenant seviyesinde yönetebilirsiniz.

---

### 4.2 Paylaşım (sharing) ayarlarının sıkılaştırılması

SharePoint varsayılan olarak paylaşım ayarlarını **en gevşek (most permissive)** seçenekle getirir. Copilot öncesinde mutlaka sıkılaştırılmalıdır.

**SharePoint Admin Center → Policies → Sharing**

- Organizasyon seviyesinde SharePoint ve OneDrive paylaşımı **"Anyone"** olarak bırakılmamalıdır.
- **File and folder links** varsayılanı **"Specific people"** olarak ayarlanmalıdır.
- Bağlantılar için **expiration** ve **permission** (view/edit) seçenekleri tanımlanmalıdır.
- Dış paylaşımın gereksiz açık olduğu Teams ve SharePoint siteleri kapatılmalıdır.
- **SharePoint Admin Center → Sites → Active sites** üzerinden sitelerin paylaşım özellikleri toplu olarak (sayfa başına seçim ile) değiştirilebilir.
- Site içindeki **Site Members → "Everyone except external users" (EEEU)** atamaları kaldırılmalıdır. EEEU, Copilot'ta aşırı paylaşımın **1 numaralı** sebebidir.

---

### 4.3 Content Management Assessment ve Data Access Governance

**SharePoint Advanced Management (SAM)** içindeki **Content Management Assessment** hub'ı, Copilot hazırlığı için rehberli bir değerlendirme çalıştırır:

- Potansiyel aşırı paylaşılan içeriği tespit eder
- Atıl (inactive) ve sahipsiz (ownerless) siteleri bulur
- Kurum için **Copilot readiness** tanımlar
- Aksiyon önerileri sunar ve tekrarlanan değerlendirmelerle ilerlemeyi izler

**Yol:** SharePoint admin center → **Advanced Management** → **Start assessment** → bulguları incele → **30 günde bir tekrarla.**

**Data Access Governance (DAG) raporları** — SharePoint admin center → **Reports → Data access governance**:

| Rapor | Ne işe yarar |
|---|---|
| **Site permissions baseline** | Tenant genelinde mevcut izin yapısının anlık görüntüsü |
| **Site permissions for users** | Bir kullanıcının erişebildiği tüm siteler |
| **Everyone except external users (EEEU)** | Son 28 günde tüm kuruma açılmış ilk 100 site |
| **Sharing links activity** | En çok "Anyone", "People in the organization" ve "Specific people" bağlantısı üretilen siteler |
| **Sensitivity label snapshot** | Etiket dağılımı |
| **Change history** | Son 180 gündeki site/tenant ayar değişiklikleri |

**AI insights:** Sharing links activity, Sensitivity label snapshot, Inactive site policy ve Change history raporlarında **Get AI insights** butonu ile bulgular yorumlanıp aksiyon önerileri üretilebilir.

---

### 4.4 Restricted Content Discovery (RCD)

**2026'nın en kritik Copilot güvenlik kontrolü.** RCD, belirli SharePoint sitelerindeki içeriğin **kurum geneli arama sonuçlarında ve Copilot yanıtlarında** görünmesini engeller.

**Ne yapar / ne yapmaz:**

| ✅ Yapar | ❌ Yapmaz |
|---|---|
| İçeriği Copilot ve organizasyon geneli aramadan gizler | İzinleri **değiştirmez** |
| Sitedeki AI giriş noktalarını kaldırır (Copilot butonu, AI actions menüsü, ajan oluşturma, Create pages with AI) | İçeriği arama indeksinden **silmez** |
| Sitede **Restricted** etiketi gösterir | Purview eDiscovery / auto-labeling'i **etkilemez** |
| İzin düzeltme çalışması için zaman kazandırır | OneDrive'da **desteklenmez** (yalnızca SharePoint siteleri) |

**Kullanım senaryoları:** aşırı paylaşım riski yüksek siteler, izin gözden geçirmesi süren siteler, kademeli Copilot dağıtımı, içerik doğrulaması gerektiren yönetişim projeleri (örn. Finans veya İK siteleri geçici olarak kapsama alınır, inceleme bitince kaldırılır).

**Ön koşullar:** SharePoint Advanced Management ve Microsoft Copilot lisansı.

**Portal üzerinden:** SharePoint admin center → **Sites → Active sites** → site → **Settings** sekmesi → **Restrict content from Microsoft Copilot: On** → Save.

**PowerShell:**

```powershell
# Etkinleştir
Set-SPOSite -Identity <site-url> -RestrictContentOrgWideSearch $true

# Kaldır
Set-SPOSite -Identity <site-url> -RestrictContentOrgWideSearch $false

# Durum kontrolü
Get-SPOSite -Identity <site-url> | Select RestrictContentOrgWideSearch

# Site yöneticilerine yetki devri (değişiklikte gerekçe zorunlu olur)
Set-SPOTenant -DelegateRestrictedContentDiscoverabilityManagement $true
Get-SPOTenant | Select-Object DelegateRestrictedContentDiscoverabilityManagement

# Tenant geneli RCD raporu
Start-SPORestrictedContentDiscoverabilityReport
Get-SPORestrictedContentDiscoverabilityReport
Get-SPORestrictedContentDiscoverabilityReport -Action Download -ReportId <ReportGUID>
```

> ⚠️ **Dikkat:** RCD **geçici bir yönetişim kontrolüdür**, kalıcı çözüm değildir. Aşırı kullanım, Copilot yanıtlarının ve kurum içi aramanın kalitesini düşürür.
>
> ⏱️ **Yayılma süresi:** Site büyüklüğüne bağlıdır. **500.000'den fazla öğe** içeren sitelerde değişikliğin tam olarak yansıması **bir haftadan uzun** sürebilir.
>
> 🔍 **Denetim:** RCD'nin açılması, kapatılması ve değişiklik gerekçeleri Purview audit log'unda kayıt altına alınır.

---

### 4.5 Restricted Access Control (RAC)

RCD keşfi kısıtlarken, **Restricted Access Control erişimin kendisini** kısıtlar. Site erişimi bir Microsoft Entra güvenlik grubu veya Microsoft 365 grubu ile sınırlandırılır; grubun dışındaki kullanıcılar daha önce izinleri veya bir bağlantıları olsa bile siteye ve içeriğine erişemez.

**Yol:** SharePoint admin center → **Policies → Access control** → **Enable site access restriction** (isterseniz **Delegate site access restriction control to site administrators**) → Save.

| | RCD | RAC |
|---|---|---|
| İzinleri değiştirir mi? | Hayır | Evet (etkin olarak kısıtlar) |
| Copilot yanıtlarını etkiler mi? | Evet (keşfi engeller) | Evet (erişim olmayan kullanıcı için içerik yok) |
| Amaç | Geçici koruma / zaman kazanma | Kalıcı erişim daraltma |

---

### 4.6 Site yaşam döngüsü, arşivleme ve atıl içerik temizliği

Atıl ve sahipsiz içerik hem risk hem de Copilot yanıt kalitesi sorunudur. Copilot'un güncel içeriğe referans vermesi için yaşam döngüsü yönetimi şarttır.

**SharePoint admin center → Site lifecycle management** altındaki politikalar:

- **Site ownership policies** — minimum sahip/yönetici sayısı belirleyip sahipsiz site riskini azaltır.
- **Inactive site policies** — atıl siteleri tespit eder ve site sahiplerine e-posta ile bildirir.
- **Site attestation policies** — site sahiplerinden site gerekliliği, sahipleri, üyeleri, izinleri ve paylaşım ayarlarını doğrulamasını ister.

**Microsoft 365 Archive:** Atıl/sahipsiz siteleri arşiv katmanına taşır. Arşivlendiğinde site aktif SharePoint depolama kotasını tüketmez; içerik, izinler ve meta veriler korunur; site yeniden etkinleştirilene kadar kullanıcılar erişemez ve **Copilot arşivlenmiş içeriği kullanmaz.**

**Microsoft Entra Groups | Expiration:** Kullanılmayan Microsoft 365 gruplarının otomatik olarak süresinin dolmasını sağlar.
**Yol:** Microsoft Entra admin center → **Groups → Settings → Expiration**

Ayrıca **Microsoft Purview Data Lifecycle Management** ile ihtiyaç duyulmayan dosyalar tespit edilip silinebilir.

---

### 4.7 Search / indexing kontrolü

SharePoint Online'ın **semantik arama dizini**, belge içeriklerini anlam düzeyinde analiz ederek daha isabetli sonuçlar üretir ve Copilot'un içerik bulma yeteneğini besler.

Belirli bir siteyi arama ve Copilot kapsamı dışında bırakmak için:

1. Uygun yönetici izinleriyle siteyi açın.
2. **Settings → Site Information** seçin.
3. **View all site settings** ile Site Settings sayfasını açın.
4. **Search** kategorisi altında **Search and offline availability** seçin; hem Microsoft Search hem de Semantic index search için **"No"** işaretleyin.

> 💡 **2026 önerisi:** Site bazında indekslemeyi tamamen kapatmak yerine, çoğu senaryoda **Restricted Content Discovery** tercih edilmelidir. RCD merkezî olarak yönetilir, raporlanabilir, denetlenebilir ve Purview kabiliyetlerini bozmaz. Indexing kapatma, arama deneyimini kalıcı olarak sakatlayabilir.

---

### 4.8 Microsoft 365 Groups yönetimi

Grup oluşturma yetkisi kontrolsüz bırakıldığında site ve ekip sayısı (sprawl) hızla artar; bu da yönetilemeyen veri havuzları demektir.

1. Microsoft 365 grubu oluşturabilecek kullanıcılar için bir **security group** oluşturun ve ilgili kullanıcıları ekleyin.
2. **Microsoft Graph PowerShell** modülünü yükleyip Microsoft'un yayımladığı script ile grup oluşturmayı bu güvenlik grubuyla sınırlandırın.

📄 [Manage who can create Microsoft 365 Groups](https://learn.microsoft.com/en-us/microsoft-365/solutions/manage-creation-of-groups)

---

### 4.9 Microsoft Teams External Access kontrolü

External Access, kullanıcıların kendi Teams ortamları dışındaki Teams kullanıcılarıyla mesajlaşmasına, arama yapmasına ve toplantılara katılmasına imkân tanır.

**Yol:** Microsoft Teams admin center → **Users → External access**

**Öneri:** "Allow all external domains" yerine **allow-list (izinli domain listesi)** modeline geçin; Guest Access ayarlarını ve konuk kullanıcıların erişebildiği siteleri ayrıca gözden geçirin.

---

## 5. Microsoft Purview ile Veri Güvenliği

Microsoft 365 Copilot ve Copilot Chat etkileşimleri için desteklenen Purview kabiliyetleri:

| Kabiliyet | AI etkileşimleri için destekli |
|---|---|
| DSPM ve DSPM for AI | ✓ |
| Auditing | ✓ |
| Data classification | ✓ |
| Sensitivity labels | ✓ |
| Etiketsiz şifreleme (Azure RMS) | ✓ |
| Data Loss Prevention | ✓ |
| Insider Risk Management | ✓ |
| Communication Compliance | ✓ |
| eDiscovery | ✓ |
| Data Lifecycle Management | ✓ |
| Compliance Manager | ✓ |

---

### 5.1 DSPM for AI — AI güvenliğinin ön kapısı

**Data Security Posture Management for AI**, kurumdaki AI kullanımını keşfetmek, korumak ve uyumluluk kontrollerini uygulamak için **başlangıç noktasıdır (front door).** 2026'da DSPM ve DSPM for AI tek bir deneyim altında birleştirilmiştir.

**Sunduğu kabiliyetler:**

- **Data risk assessments** — aşırı paylaşım riskine yol açabilecek sorunları tespit eder (haftalık varsayılan değerlendirme + özel değerlendirmeler).
- **Activity explorer / AI activities** — prompt ve yanıt metinlerini, grounding sırasında kullanılan web sorgularını ve referans verilen dosyaları gösterir *(Content Explorer Content Viewer rolü gerekir).*
- **Tek tıkla politikalar (one-click policies):**
  - *Protect your data with sensitivity labels*
  - *Detect risky interactions in AI apps*
  - *Detect unethical behavior in AI*
  - *Protect items with sensitivity labels from Microsoft 365 Copilot and agent processing*
  - *Detect sensitive info shared with AI via network*
- **Reports → Copilot experiences & agents** — toplam etkileşim, AI uygulaması başına hassas etkileşim, en çok referans verilen sensitivity label'lar, insider risk şiddeti vb.

**Başlangıç adımları:**

1. Microsoft Purview portalı → **Solutions → DSPM for AI** (uygun yetkili hesapla, ör. Compliance Administrator).
2. **Overview → All AI apps → Get Started** bölümünde denetimin (audit) açık olduğunu doğrulayın; değilse **Activate Microsoft Purview Audit**.
3. Görünümü **Microsoft 365 Copilot**'a çevirip şu bölümleri sırayla uygulayın: *Assess and prevent oversharing of sensitive data* → *Secure your data in Microsoft 365 Copilot* → *Discover Microsoft 365 Copilot activity*.
4. **Recommendations** altından tek tıkla politikaları etkinleştirin.
5. **En az 1 gün** bekleyip **Reports** sayfasından sonuçları izleyin.

---

### 5.2 Sensitivity Label ve şifreleme

Microsoft Purview Information Protection ile veriler sınıflandırılırken şifreleme de uygulanabilir. Şifrelenmiş veride Copilot, kullanıcıya verilen kullanım haklarını doğrular:

- Kullanıcının **VIEW** ve **EXTRACT** haklarına sahip olması gerekir; aksi hâlde içerik Copilot yanıtında kullanılmaz.
- Bu koruma, Office uygulamasında açılan **tenant dışındaki** verilere de uzanır (yerel disk, ağ paylaşımı, bulut depolama).

**Sensitivity label inheritance (etiket kalıtımı):** Word, PowerPoint ve Outlook'ta Copilot ile etiketli bir kaynaktan yeni içerik üretildiğinde, kaynak dosyanın etiketi ve koruma ayarları **otomatik olarak devralınır.** Birden fazla dosya kullanıldığında **en yüksek öncelikli** etiket uygulanır.

Copilot Chat yanıt ve alıntılarında öğelerin etiketi gösterilir; en son yanıt ve Teams sohbet/kanal özetlerinde kullanılan veriler içindeki **en yüksek öncelikli (en kısıtlayıcı)** etiket görüntülenir.

> ⚠️ **Kritik ön koşul:** SharePoint ve OneDrive için sensitivity label desteğini etkinleştirmediyseniz, Copilot'un erişebildiği şifreli dosyalar yalnızca Windows üzerindeki Office uygulamalarında **açık olan** verilerle sınırlı kalır.
> 📄 [Enable sensitivity labels for Office files in SharePoint and OneDrive](https://learn.microsoft.com/en-us/purview/sensitivity-labels-sharepoint-onedrive-files)

Ayrıca **site sensitivity label**'ları ile Teams, Microsoft 365 Groups ve SharePoint sitelerinde konteyner seviyesinde koruma uygulanabilir.

---

### 5.3 DLP for Microsoft 365 Copilot

Purview DLP'de artık **"Microsoft 365 Copilot and Copilot Chat"** özel bir **policy location**'dır. Bu konumla:

- Belirli **sensitive information type** içeren prompt'ların işlenmesi kısıtlanabilir,
- Belirli **sensitivity label** taşıyan dosya ve e-postaların Copilot tarafından işlenmesi/yanıtta kullanılması engellenebilir.

**Endpoint DLP** tarafında ise (Purview'a onboard edilmiş Windows cihazlarda) kullanıcıların üçüncü parti üretken AI sitelerine hassas veri yapıştırması uyarılabilir veya engellenebilir. Copilot Chat (web sürümü) için **Block paste of sensitive content** ve **Block files based on a specified sensitivity label** desteklenir.

---

### 5.4 Insider Risk Management ve Adaptive Protection

**Risky AI usage** politika şablonu ile prompt injection denemeleri ve korunan materyale erişim gibi riskli kullanımlar tespit edilir. Sinyaller **Microsoft Defender XDR** ile entegre çalışır.

**Adaptive Protection** sayesinde risk seviyesi yükselen kullanıcılar otomatik olarak daha katı politikalara alınabilir; yüksek riskli kullanıcıların Copilot ve ajanlar üzerinden hassas içeriğe erişimi engellenebilir.

---

### 5.5 Audit (denetim kayıtları)

Copilot'a ait tüm prompt ve yanıtlar, diğer aktiviteler gibi **unified audit log**'da kaydedilir. Olaylar şunları içerir:

- Kullanıcıların Copilot ile **nasıl ve ne zaman** etkileşime girdiği,
- Etkinliğin hangi Microsoft 365 hizmetinde gerçekleştiği,
- Etkileşim sırasında erişilen dosyalara yapılan referanslar,
- Bu dosyalara uygulanmış **sensitivity label** bilgisi.

Bu olaylar hem **Purview Audit** aramalarında hem de **DSPM for AI → activity explorer → AI activities** sekmesinde incelenebilir.

---

### 5.6 Retention / Data Lifecycle Management

Copilot prompt ve yanıtları için saklama/silme politikaları uygulanabilir.

- Retention policy oluştururken **"Microsoft Copilot Experiences"** konumunu seçin.
- Retention policy, **Teams shared channels**'ı destekler; paylaşılan kanallar saklama ayarlarını üst ekipten devralır.
- Toplantı ve aramalara ait meta veri içeren sistem mesajları (**call data records**) da desteklenir ve her zaman *Teams chats and Copilot interactions* konumuna dâhil edilir.
- **Cloud attachments** için auto-apply retention label kullanıldığında, Copilot'ta referans verilen dosyaların **o andaki sürümü** saklanır.
- Birden fazla politika çakışırsa **retention prensipleri** uygulanır (en uzun süre kazanır).

---

### 5.7 Communication Compliance

Communication Compliance, çalışan iletişimlerini politika ve mevzuata uygunluk açısından izleyerek uygunsuz davranış, veri sızıntısı, gizlilik ihlali ve yasal riskleri proaktif tespit eder. Varsayılan olarak **gizlilik odaklıdır**: kullanıcı adları pseudonymize edilir ve rol tabanlı erişim uygulanır.

Copilot prompt ve yanıtları da politikalarla analiz edilebilir. Algılanabilen Copilot deneyimleri arasında Word, Excel, PowerPoint, Outlook, OneNote, Loop, Forms, Planner, Stream, Whiteboard Copilot ile Teams (sohbet/kanal/toplantı) ve Microsoft 365 Chat yer alır.

📄 [Configure a communication compliance policy to detect for generative AI interactions](https://learn.microsoft.com/en-us/purview/communication-compliance-copilot)

---

### 5.8 eDiscovery — Copilot verisini arama ve silme

Copilot prompt ve yanıtları kullanıcının posta kutusunda saklandığı için eDiscovery ile aranabilir, hukuki muhafazaya (legal hold) alınabilir, dışa aktarılabilir ve silinebilir.

**Sorgu oluştururken:** query builder → **Add condition → Type → Contains any of → Edit → Copilot activity**. Bu koşul tüm Copilot ve diğer AI uygulama aktivitelerini kapsar.

**Gerekli roller:** **eDiscovery Manager** rol grubu üyeliği; veri silmek için **Search and Purge** rolü.

Bu akış, gizli veya kötü amaçlı içeriğin Copilot etkinliği aracılığıyla yayılması durumunda **veri sızıntısı olayına müdahale** için de kullanılır.

---

### 5.9 Compliance Manager

Compliance Manager, üretken AI uygulamaları için **düzenleyici şablonlar (regulatory templates)** sunar; AI mevzuatlarına uyum durumunuzu değerlendirmenize, kontrolleri uygulamanıza ve denetçilere raporlamanıza yardımcı olur. DSPM for AI içindeki *"Get guided assistance to AI regulations"* önerisi doğrudan bu şablonları kullanır.

---

## 6. Defender for Cloud Apps ile Public/External Dosya Yönetimi

Dosya izleme (file monitoring) devreye alınarak, belirli hassasiyet seviyesindeki dosyaların dış kullanıcılarla paylaşılması durumunda erişimin **otomatik kaldırılması** sağlanabilir.

**Yapılandırma:**

1. <https://security.microsoft.com> adresine yetkili hesabınızla giriş yapın.
2. Sol panelden **System → Settings → Cloud Apps** adımlarını izleyin.
3. **Connected Apps → App Connectors → Connect an app** ile Microsoft 365 ve Microsoft Azure loglarını Defender for Cloud Apps'e yönlendirin.
4. Bağlantı tamamlandıktan **yaklaşık 24 saat** sonra kullanıcı ve uygulama aktivite raporlarını görmeye başlarsınız.
5. **Files** bölümünde **Access Level → Public** filtresiyle herkese açık paylaşılan dokümanların raporunu alın.
6. **New policy from Search** ile özel bir dosya politikası oluşturun. Policy template seçilmezse **File matching all the following** koşulları tanımlanabilir.
7. **Governance Actions** bölümünde **Remove external users** aksiyonunu politikaya dâhil ederek public/external paylaşımları otomatik kaldırın.

---

## 7. Ajan (Agent) Yönetişimi — 2026'nın Yeni Gündemi

Copilot artık tek bir asistan değil; **ajanlardan oluşan bir ekosistem.** Declarative agent'lar (Agent Builder), Copilot Studio ajanları ve Microsoft 365 hazır ajanları aynı veri sınırında çalışır ve **aynı izin modeline tabidir.** Bu nedenle ajan yönetişimi, Copilot veri güvenliğinin ayrılmaz bir parçasıdır.

**Microsoft 365 admin center → Copilot Control System** üzerinden yönetilmesi gerekenler:

- **Agent access & availability policies** — hangi kullanıcıların hangi ajanlara erişeceği, ajanların paylaşılıp yayımlanabilirliği.
- **Agent inventory & lifecycle** — kurumda mevcut ajanların envanteri, sahiplik ataması, yaşam döngüsü ve emekliye ayırma.
- **Oluşturma yetkisi** — Agent Builder ile declarative agent oluşturabilecek kullanıcıların sınırlandırılması.
- **Copilot Studio ALM** — geliştirme/test/üretim ortam ayrımı ve zone yönetişimi.
- **Veri erişimi** — ajanların bağlandığı SharePoint siteleri, connector'lar ve dış veri kaynakları. RCD uygulanmış sitelerde ajan oluşturma giriş noktaları da kaldırılır.
- **Pay-as-you-go / Copilot Capacity Pack** — maliyet kontrolü ve tüketim izleme.

> 🔑 **Altın kural:** Bir ajan, oluşturucusunun veya kullanıcısının yetkilerinin ötesine geçemez. Ancak **paylaşılan bir ajan**, dar bir kitlenin bilmediği içeriği geniş kitleye görünür kılabilir. Ajan yayımlamayı onay sürecine bağlayın.

---

## 8. Yedekleme ve Kurtarma

Kazara veya kötü niyetli silme/üzerine yazma senaryolarına karşı **Microsoft 365 Backup** değerlendirilmelidir:

- Veri, Microsoft 365 **data trust boundary**'sinin dışına çıkmaz ve mevcut veri ikamet (data residency) coğrafyanıza uyar.
- Yedekler, yedekleme yöneticisi açıkça silmediği sürece **değiştirilemez (immutable)**.
- OneDrive, SharePoint ve Exchange için fiziksel olarak yedekli çoklu kopyalar bulunur.

**Yol:** Microsoft 365 admin center → **Settings → Microsoft 365 Backup**

---

## 9. Kontrol Listesi (Checklist)

> Aşağıdaki maddeleri Copilot dağıtımı öncesinde (**Pre-Deployment**) ve sonrasında (**Operate**) uygulayın. `Öncelik`: 🔴 Kritik · 🟠 Yüksek · 🟡 Orta

### A. Kimlik ve Erişim

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| A1 | Copilot lisansları dalga bazlı dağıtıldı; pilot grup tanımlandı | M365 admin center | 🟠 | ☐ |
| A2 | Copilot ve ajan erişim politikaları tenant seviyesinde tanımlandı | Copilot Control System | 🟠 | ☐ |
| A3 | Ayrıcalıklı hesaplar için MFA / Conditional Access uygulanıyor | Entra ID | 🔴 | ☐ |
| A4 | Konuk (guest) kullanıcı envanteri çıkarıldı ve gereksizler kaldırıldı | Entra ID | 🟠 | ☐ |

### B. SharePoint / OneDrive Paylaşım Hijyeni

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| B1 | Organizasyon seviyesi paylaşım **"Anyone"** değil | SPO Admin → Policies → Sharing | 🔴 | ☐ |
| B2 | File and folder links varsayılanı **"Specific people"** | SPO Admin → Policies → Sharing | 🔴 | ☐ |
| B3 | Paylaşım bağlantılarına **expiration** ve izin (view/edit) sınırı tanımlı | SPO Admin → Policies → Sharing | 🟠 | ☐ |
| B4 | **EEEU (Everyone except external users)** atamaları tespit edilip kaldırıldı | DAG → EEEU raporu | 🔴 | ☐ |
| B5 | Gereksiz dış paylaşıma açık Teams/SharePoint siteleri kapatıldı | SPO Admin → Active sites | 🔴 | ☐ |
| B6 | OneDrive **"Anyone"** bağlantıları raporlandı ve temizlendi | DAG → Sharing links activity | 🟠 | ☐ |

### C. Değerlendirme ve Raporlama

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| C1 | **Content Management Assessment** çalıştırıldı | SPO Admin → Advanced Management | 🔴 | ☐ |
| C2 | Assessment **30 günde bir** tekrarlanacak şekilde planlandı | Operasyon takvimi | 🟠 | ☐ |
| C3 | **Site permissions baseline** raporu incelendi | DAG raporları | 🔴 | ☐ |
| C4 | **Sharing links activity** ve **Change history** raporları düzenli izleniyor | DAG / Reports | 🟠 | ☐ |
| C5 | Raporlarda **AI insights** kullanılarak aksiyon listesi çıkarıldı | DAG raporları | 🟡 | ☐ |
| C6 | **SharePoint Admin Agent** ile tenant içerik dağılımı analiz edildi | SPO Admin / Teams | 🟡 | ☐ |

### D. Erişim ve Keşif Kısıtlama

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| D1 | Yüksek riskli siteler listesi oluşturuldu (Anyone link + hassas veri + sahipsiz vb.) | DAG + AI insights | 🔴 | ☐ |
| D2 | Yüksek riskli sitelere **Restricted Content Discovery** uygulandı | SPO Admin → Site → Settings | 🔴 | ☐ |
| D3 | RCD kapsamının **geçici** olduğu ve çıkış planı (izin düzeltme) tanımlandı | Yönetişim planı | 🟠 | ☐ |
| D4 | RCD tenant raporu periyodik alınıyor (`Start-SPORestrictedContentDiscoverabilityReport`) | PowerShell | 🟡 | ☐ |
| D5 | Kritik siteler için **Restricted Access Control** yapılandırıldı | SPO Admin → Policies → Access control | 🟠 | ☐ |
| D6 | RCD/RAC değişiklikleri audit log'da izleniyor | Purview Audit | 🟡 | ☐ |

### E. İçerik Yaşam Döngüsü

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| E1 | **Site ownership policy** tanımlı (minimum sahip sayısı) | Site lifecycle management | 🟠 | ☐ |
| E2 | **Inactive site policy** aktif | Site lifecycle management | 🟠 | ☐ |
| E3 | **Site attestation policy** ile sahiplere periyodik doğrulama gönderiliyor | Site lifecycle management | 🟡 | ☐ |
| E4 | Atıl siteler **Microsoft 365 Archive** ile arşivlendi | SPO Admin → Archived sites | 🟡 | ☐ |
| E5 | Microsoft 365 **Groups expiration** politikası aktif | Entra → Groups → Expiration | 🟠 | ☐ |
| E6 | Grup oluşturma yetkisi bir güvenlik grubuyla sınırlandırıldı | Graph PowerShell | 🟠 | ☐ |
| E7 | Gereksiz dosyalar **Data Lifecycle Management** ile temizleniyor | Purview | 🟡 | ☐ |

### F. Purview — Veri Koruma

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| F1 | **Unified audit log** açık | Purview Audit | 🔴 | ☐ |
| F2 | **DSPM for AI** etkin ve Microsoft 365 Copilot görünümü incelendi | Purview → DSPM for AI | 🔴 | ☐ |
| F3 | **Data risk assessment** (haftalık + özel) çalıştırıldı | DSPM for AI | 🔴 | ☐ |
| F4 | Sensitivity label taksonomisi tanımlı ve yayımlandı | Purview Information Protection | 🔴 | ☐ |
| F5 | SharePoint & OneDrive için **sensitivity label desteği etkinleştirildi** | Purview | 🔴 | ☐ |
| F6 | Hassas etiketlerde **şifreleme + kullanım hakları (VIEW/EXTRACT)** yapılandırıldı | Etiket → Protection | 🔴 | ☐ |
| F7 | Teams / Groups / SharePoint için **site (container) etiketleri** uygulandı | Purview | 🟠 | ☐ |
| F8 | Otomatik etiketleme (auto-labeling) politikaları devrede | Purview | 🟠 | ☐ |
| F9 | **DLP → Microsoft 365 Copilot and Copilot Chat** policy location tanımlı | Purview DLP | 🔴 | ☐ |
| F10 | **Endpoint DLP** ile 3. parti AI sitelerine hassas veri yapıştırma engelleniyor | Purview DLP | 🟠 | ☐ |
| F11 | **"Protect items with sensitivity labels from Copilot processing"** politikası aktif | DSPM for AI one-click | 🟠 | ☐ |

### G. Purview — İzleme, Uyum ve Müdahale

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| G1 | **Risky AI usage** Insider Risk politikası oluşturuldu | Purview IRM | 🟠 | ☐ |
| G2 | **Adaptive Protection** ile yüksek riskli kullanıcılar kısıtlanıyor | Purview IRM | 🟡 | ☐ |
| G3 | Copilot etkileşimleri için **Communication Compliance** politikası tanımlı | Purview CC | 🟠 | ☐ |
| G4 | **Retention policy → "Microsoft Copilot Experiences"** yapılandırıldı | Purview DLM | 🟠 | ☐ |
| G5 | Cloud attachment için auto-apply retention label tanımlı | Purview DLM | 🟡 | ☐ |
| G6 | eDiscovery rolleri atandı (**eDiscovery Manager**, **Search and Purge**) | Purview | 🟠 | ☐ |
| G7 | **Type → Copilot activity** sorgusuyla arama/silme prosedürü test edildi | Purview eDiscovery | 🟠 | ☐ |
| G8 | **Compliance Manager** AI regulation assessment'ı başlatıldı | Purview | 🟡 | ☐ |
| G9 | DSPM for AI **Reports → Copilot experiences & agents** periyodik gözden geçiriliyor | Purview | 🟠 | ☐ |

### H. Teams, Defender ve Ajanlar

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| H1 | Teams **External Access** allow-list modeline geçirildi | Teams admin center | 🟠 | ☐ |
| H2 | Defender for Cloud Apps **app connector** bağlandı | security.microsoft.com | 🟠 | ☐ |
| H3 | **Access Level = Public** dosya raporu alınıyor | Defender for Cloud Apps → Files | 🟠 | ☐ |
| H4 | **Remove external users** governance action içeren dosya politikası oluşturuldu | Defender for Cloud Apps | 🟠 | ☐ |
| H5 | Ajan envanteri çıkarıldı ve sahipleri atandı | Copilot Control System | 🔴 | ☐ |
| H6 | Ajan oluşturma / paylaşma / yayımlama yetkileri sınırlandırıldı | Copilot Control System | 🔴 | ☐ |
| H7 | Ajanların bağlandığı veri kaynakları ve connector'lar gözden geçirildi | Copilot Studio / CCS | 🟠 | ☐ |
| H8 | Pay-as-you-go tüketimi ve kapasite izleniyor | CCS | 🟡 | ☐ |

### I. Süreklilik

| # | Kontrol Maddesi | Nerede | Öncelik | Durum |
|---|---|---|---|---|
| I1 | **Microsoft 365 Backup** yapılandırıldı | M365 admin center → Settings | 🟠 | ☐ |
| I2 | Copilot veri sızıntısı senaryosu için müdahale prosedürü (search & purge) yazıldı | IR playbook | 🟠 | ☐ |
| I3 | Kullanıcı farkındalık eğitimi (etiketleme, paylaşım, AI kullanımı) verildi | Eğitim | 🟠 | ☐ |
| I4 | Kurumsal **AI kullanım politikası** yayımlandı | Politika | 🔴 | ☐ |

---

## 10. Faydalı PowerShell Komutları

### Atıl SharePoint sitelerini listeleme

```powershell
Install-Module -Name Microsoft.Online.SharePoint.PowerShell
Import-Module Microsoft.Online.SharePoint.PowerShell

Connect-SPOService -Url https://<tenant>-admin.sharepoint.com/

Get-SPOSite -Limit All |
    Select-Object Title, Url, Template, StorageQuota, LastContentModifiedDate,
                  LockState, SharingCapability, Owner |
    Out-GridView

Disconnect-SPOService
```

> `LastContentModifiedDate` alanına bakarak atıl siteleri belirleyebilirsiniz.

### Dış paylaşıma açık siteleri bulma

```powershell
Get-SPOSite -Limit All |
    Where-Object { $_.SharingCapability -ne "Disabled" } |
    Select-Object Url, SharingCapability, LastContentModifiedDate |
    Export-Csv .\ExternalSharingSites.csv -NoTypeInformation
```

### RCD durumunu toplu raporlama

```powershell
Get-SPOSite -Limit All |
    Select-Object Url, RestrictContentOrgWideSearch |
    Export-Csv .\RCD-Status.csv -NoTypeInformation
```

### Anonim (Anyone) link aktivite raporu

OneDrive için 90 günlük "Anyone" link paylaşımlarını görmek üzere topluluk script'i kullanılabilir:

```powershell
.\AnonymousLinkActivityReport.ps1 -OneDrive -AnonymousSharing
```

> ℹ️ Aynı bilgiyi artık **SPO Admin → Reports → Data access governance → Sharing links activity** raporundan da doğrudan alabilirsiniz.

---

## 11. Kaynaklar

- [Copilot Control System — Security and governance](https://learn.microsoft.com/en-us/microsoft-365/copilot/copilot-control-system/security-governance)
- [Get ready for Microsoft Copilot and agents with SharePoint Advanced Management](https://learn.microsoft.com/en-us/microsoft-365/copilot/get-ready-copilot-sharepoint-advanced-management)
- [Restrict discovery of SharePoint sites and content (RCD)](https://learn.microsoft.com/en-us/sharepoint/restricted-content-discovery)
- [Use Microsoft Purview to manage data security & compliance for Microsoft 365 Copilot](https://learn.microsoft.com/en-us/purview/ai-m365-copilot)
- [Microsoft Purview data security and compliance protections for AI apps](https://learn.microsoft.com/en-us/purview/ai-microsoft-purview)
- [Agents governance visual guide for Microsoft 365](https://learn.microsoft.com/en-us/microsoft-365/copilot/agent-essentials/m365-agents-visual-map)
- [Enable sensitivity labels for Office files in SharePoint and OneDrive](https://learn.microsoft.com/en-us/purview/sensitivity-labels-sharepoint-onedrive-files)
- [Manage who can create Microsoft 365 Groups](https://learn.microsoft.com/en-us/microsoft-365/solutions/manage-creation-of-groups)
- [Restrict access to a SharePoint site](https://learn.microsoft.com/en-us/sharepoint/restricted-access-control)
- [Manage sharing settings for SharePoint and OneDrive](https://learn.microsoft.com/en-us/sharepoint/turn-external-sharing-on-or-off)
- [Overview of Microsoft 365 Backup](https://learn.microsoft.com/en-us/microsoft-365/backup/backup-overview)
- [Use Zero Trust security to prepare for Copilot](https://learn.microsoft.com/en-us/security/zero-trust/copilots/zero-trust-microsoft-365-copilot)

---

## Özet

Microsoft 365 Copilot, üretkenliği yapay zekânın gücüyle yeniden tanımlarken kurumsal güvenliği bu sürecin merkezine yerleştirir. Microsoft Graph ve Microsoft 365 uygulamalarıyla sağlanan derin entegrasyon sayesinde, karmaşık veri yığınları arasında **yalnızca yetkiniz dâhilindeki** bilgilerle anlamlı sonuçlar üretir.

**"On behalf of"** prensibiyle hareket eden bu sistem, kurumun mevcut erişim kontrollerine ve uyumluluk politikalarına tam sadakat gösterir. Ancak bu sadakat çift yönlüdür: **izin hijyeniniz ne kadar iyiyse Copilot o kadar güvenlidir.**

2026 itibarıyla doğru yaklaşım şudur:

1. **Ölç** — Content Management Assessment + DAG raporları + DSPM for AI risk değerlendirmeleri
2. **Sınırla** — Sharing ayarları, EEEU temizliği, RCD ve RAC
3. **Koru** — Sensitivity label + şifreleme + DLP for Copilot
4. **İzle** — Audit, Insider Risk, Communication Compliance, DSPM raporları
5. **Yönet** — Site yaşam döngüsü, ajan envanteri, retention ve yedekleme

Bu beş adım tamamlandığında Copilot, verilerinizin gizliliğini koruyarak iş akışlarınızı geleceğe taşıyan güvenilir bir yardımcı pilot hâline gelir.

---

<sub>Bu doküman bilgilendirme amaçlıdır. Microsoft ürün özellikleri hızla değiştiğinden, uygulamadan önce ilgili Microsoft Learn sayfalarındaki güncel bilgiyi doğrulayın.</sub>

<sub>© Emre ERBULMUŞ — Katkı ve düzeltmeler için pull request açabilirsiniz.</sub>
