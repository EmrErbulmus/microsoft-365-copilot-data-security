# Microsoft 365 Copilot Data Security

> Microsoft 365 Copilot dağıtımı öncesinde ve sonrasında uygulanması gereken veri güvenliği kontrollerinin uçtan uca rehberi — **50 maddelik uygulanabilir kontrol listesi** ile birlikte.

![Last update](https://img.shields.io/badge/G%C3%BCncelleme-Eyl%C3%BCl%202026-blue)
![Language](https://img.shields.io/badge/Dil-T%C3%BCrk%C3%A7e-red)
![Topic](https://img.shields.io/badge/Konu-Microsoft%20365%20Copilot-0078D4)
![Topic](https://img.shields.io/badge/Konu-Microsoft%20Purview-742774)

---

## 📖 Rehbere git

### **[→ Microsoft 365 Copilot Data Security (tam doküman)](./microsoft-365-copilot-data-security.md)**

---

## Neden bu rehber?

Microsoft 365 Copilot bir **lisans projesi değil, bir veri yönetişimi projesidir.**

Copilot, kullanıcı adına (*on behalf of*) çalışır ve yalnızca kullanıcının zaten erişebildiği verilerle yanıt üretir. Bu güçlü bir güvenlik garantisidir — ama aynı zamanda şu anlama gelir:

> **Copilot, mevcut izin hijyeninizin aynasıdır.** Ortamınızda aşırı paylaşım (oversharing) varsa, Copilot bunu görünür ve aranabilir hâle getirir.

Bu doküman, Copilot'u güvenle devreye almak için gereken tüm yapılandırma adımlarını sırasıyla ve uygulanabilir biçimde anlatır.

---

## İçerik

| Bölüm | Kapsam |
|---|---|
| **Copilot nasıl çalışır** | "On behalf of" prensibi, veri sınırı, VIEW/EXTRACT hakları |
| **Copilot Control System (CCS)** | Foundational (E3) ve Optimized (E5) kontrol ayrımı |
| **Dağıtım öncesi adımlar** | Sharing hijyeni, EEEU temizliği, DAG raporları, Content Management Assessment |
| **Restricted Content Discovery** | Portal + PowerShell, delegasyon, raporlama, yayılma süreleri |
| **Restricted Access Control** | Kalıcı erişim daraltma ve RCD ile farkı |
| **İçerik yaşam döngüsü** | Site lifecycle policies, Microsoft 365 Archive, Groups expiration |
| **Microsoft Purview** | DSPM for AI, sensitivity label, DLP for Copilot, Insider Risk, Audit, Retention, Communication Compliance, eDiscovery |
| **Defender for Cloud Apps** | Public/external dosya politikaları ve otomatik remediation |
| **Ajan (agent) yönetişimi** | Envanter, oluşturma/yayımlama yetkileri, veri erişimi |
| **Kontrol listesi** | 9 kategori · 50 madde · öncelik seviyeli · işaretlenebilir |
| **PowerShell** | Atıl site, dış paylaşım ve RCD raporlama komutları |

---

## Kontrol listesi nasıl kullanılır?

Rehberin [9. bölümü](./microsoft-365-copilot-data-security.md#9-kontrol-listesi-checklist) dokuz kategoride 50 kontrol maddesi içerir. Her madde için **nerede yapılacağı**, **öncelik seviyesi** ve işaretlenebilir bir durum kutusu bulunur.

| Kategori | Madde |
|---|---|
| A — Kimlik ve Erişim | 4 |
| B — SharePoint/OneDrive Paylaşım Hijyeni | 6 |
| C — Değerlendirme ve Raporlama | 6 |
| D — Erişim ve Keşif Kısıtlama | 6 |
| E — İçerik Yaşam Döngüsü | 7 |
| F — Purview: Veri Koruma | 11 |
| G — Purview: İzleme, Uyum ve Müdahale | 9 |
| H — Teams, Defender ve Ajanlar | 8 |
| I — Süreklilik | 4 |

**Öncelik:** 🔴 Kritik · 🟠 Yüksek · 🟡 Orta

Dosyayı repo'dan indirip kendi ortamınız için doldurabilir, bulgularınızı takım içinde paylaşabilirsiniz.

---

## 5 adımda özet yaklaşım

1. **Ölç** — Content Management Assessment, Data Access Governance raporları, DSPM for AI risk değerlendirmeleri
2. **Sınırla** — Sharing ayarları, EEEU temizliği, Restricted Content Discovery, Restricted Access Control
3. **Koru** — Sensitivity label + şifreleme + DLP for Copilot
4. **İzle** — Audit, Insider Risk, Communication Compliance, DSPM raporları
5. **Yönet** — Site yaşam döngüsü, ajan envanteri, retention ve yedekleme

---

## Kimler için?

- Microsoft 365 / SharePoint yöneticileri
- Siber güvenlik ve uyumluluk ekipleri
- Copilot dağıtımını planlayan BT proje yöneticileri
- Microsoft Purview ile veri koruma yürüten ekipler

---

## Katkı

Düzeltme, ekleme veya güncelleme önerileriniz için **issue** açabilir ya da **pull request** gönderebilirsiniz. Microsoft ürün özellikleri hızla değiştiği için güncel kalması topluluk katkısıyla mümkün.

---

## Yazar

**Emre ERBULMUŞ** — Siber Güvenlik Alan Uzmanı

🌐 [emreerbulmus.com](https://www.emreerbulmus.com) · 📄 [Orijinal makale](https://www.emreerbulmus.com/microsoft-365-copilot-data-security/)

---

<sub>Bu doküman bilgilendirme amaçlıdır ve kişisel görüşleri yansıtır; çalışılan kurumu bağlamaz. Uygulamadan önce ilgili Microsoft Learn sayfalarındaki güncel bilgiyi doğrulayın.</sub>
