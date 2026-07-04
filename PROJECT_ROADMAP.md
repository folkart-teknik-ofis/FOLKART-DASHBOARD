# Portföy Yönetimi Veri Platformu
## Eğitim Kaynağı + Antigravity Çalışma Kaynağı (Tek Doküman)

> **Bu doküman iki işi birden yapar:**
> 1. **Eğitim kaynağı** — insan (sen) için: loop nasıl çalışır, hangi aşamada ne yaparız, neden yaparız.
> 2. **Antigravity kaynağı** — agent için: her loop'ta okunacak, uygulanacak, işaretlenecek tek doğruluk kaynağı.
>
> **Kullanım:** Bu dosya Antigravity repo'sunun kökünde `AGENTS.md` olarak durur. Agent her loop başında BÖLÜM 0–3'ü okur, DURUM tablosuna bakar, sıradaki aşamaya geçer, uygular, DURUM'u günceller, onay bekler.

---

# BÖLÜM 0 — DURUM TABLOSU (agent her adım sonunda burayı günceller)

**ŞU AN:** Proje başlangıcı. Hiçbir aşamaya başlanmadı.

| # | Aşama | Amaç | Kod yazılır mı? | Durum | Tarih |
|---|-------|------|-----------------|-------|-------|
| 1 | Kod Sağlık Kontrolü | Mevcut durumu anla | ❌ Sadece analiz | `[ ]` | |
| 2 | Karar Soruları | Eksik kararları çıkar | ❌ Sadece soru | `[ ]` | |
| 3 | Veri Modeli + Service + EVM Motoru | Çekirdek iskelet | ✅ | `[ ]` | |
| 4 | Excel Import + Mapping + Validation | Veri girişi | ✅ | `[ ]` | |
| 5 | Dashboard Veri Bağlantısı | Plan+Actual birleşimi | ✅ | `[ ]` | |
| 6 | PDF + Excel Rapor Export | Çıktı | ✅ | `[ ]` | |
| 7 | Staging Deploy | Demo yayını (mock veri) | ✅ | `[ ]` | |
| 8 | Auth + Rol + Yetki | Kurumsal güvenlik | ✅ | `[ ]` | |
| 9 | Firestore Kalıcı Veri | Gerçek veri katmanı | ✅ | `[ ]` | |
| 10 | Production Deploy | GERÇEK canlıya alma | ✅ | `[ ]` | |
| 11 | ERTELENMİŞ — AI Chat + Eval | Soru-cevap + doğruluk testi | ✅ | `[ ]` | |
| 12 | ERTELENMİŞ — Görsel Pass (ayrı loop) | Tasarım iyileştirme | ✅ | `[ ]` | |

**Durum işaretleri:** `[ ]` başlanmadı · `[~]` devam ediyor · `[x]` bitti (TAMAM alındı)

**Bekleyen kullanıcı kararları:**
- [ ] **EAC metodu** (bkz. BÖLÜM 3) — varsayılan `BAC/CPI` seçili, kesin onay bekliyor.

---

# BÖLÜM 1 — LOOP METODOLOJİSİ (EĞİTİM)

## 1.1 Neden loop?

AI agent'a "şu sistemi kur" dersen, hepsini tek seferde üretmeye çalışır: yüzlerce dosya, kontrol edemeyeceğin kadar değişiklik, ve sonunda "neyin bozulduğunu bilmiyorum" durumu. Loop bunu engeller. Her turda **küçük bir parça** yapılır, doğrulanır, kapatılır, sonra bir sonraki parçaya geçilir. Verify-then-close.

Loop'un kalbi şu: **agent kendi işini bitmiş sanamaz.** Her turun sonunda ölçülebilir bir "bitti" kriteri (Definition of Done) ve bir "dur" koşulu (STOP) vardır. Onay alınmadan sonraki tura geçilmez.

## 1.2 Standart Loop (her aşamada aynen uygulanır)

```
1.  GIT CHECKPOINT
    Aşamaya başlamadan: git add -A && git commit -m "checkpoint: asama-X oncesi"
    Bozulursa: git revert ile geri dön. (Bu, "bozma" ricasının GARANTİSİDİR.)

2.  İNCELE
    Mevcut kodu / dosyaları oku. Neyin var olduğunu tespit et.

3.  EKSİKLERİ LİSTELE
    Ne eksik, ne yanlış, ne mock, ne riskli.

4.  ALTERNATİFLERİ DEĞERLENDİR
    En az bir alternatif düşün, en doğrusunu seç, GEREKÇESİNİ yaz.

5.  KÜÇÜK PARÇA HALİNDE UYGULA
    Bir mega-adım = yanlış. Aşama birden çok alt-adıma bölünmüşse tek tek yap.

6.  IDEMPOTENCY KONTROLÜ
    Dosya oluşturmadan önce VAR MI bak. Varsa ÜZERİNE YAZMA — raporla ve sor.

7.  BUILD / TEST
    npm run build çalıştır. Hata varsa düzelt.

8.  RAPORLA
    Aşamanın çıktı formatında ne yaptığını yaz.

9.  DURUM GÜNCELLE
    BÖLÜM 0 tablosunda ilgili satırı güncelle.

10. REGRESSION GUARD
    Bir önceki aşamanın smoke test'ini tekrar çalıştır. Bozulmadığını doğrula.

11. ONAY BEKLE
    Kullanıcı "TAMAM" demeden sonraki aşamaya GEÇME.
```

## 1.3 STOP Koşulları (her zaman geçerli — DUR ve SOR)

- `npm run build` kırılırsa
- Mevcut çalışan bir sayfa/component bozulursa
- Bir kararda belirsizlik varsa (varsayımla ilerleme — sor)
- Bir dosya zaten varsa ve üzerine yazılması gerekiyorsa
- Gerçek secret / API key yazılması gerekiyorsa (asla yazma, dur)
- Büyük refactor gerekiyorsa (küçük parça kuralını bozar — dur, planı böl)

## 1.4 Fikir Yönetimi (eğitim — 2E/DEHB çalışma tarzı için)

Aşama sırasında yeni fikir çıkarsa, agent onu reddetmez ama **hemen uygulamaz.** Üç kategoriye ayırır:

- **🔥 Şimdi** — mevcut aşamanın parçası, doğrudan hedefe hizmet ediyor.
- **⏳ Sonra** — değerli ama şimdiki aşamayı yavaşlatır. Not edilir, ilgili sonraki aşamaya taşınır.
- **💡 Fikir Havuzu** — v2+ konusu. `docs/fikir-havuzu.md` içine yazılır, unutulmaz ama çekirdeği bloklamaz.

Kritik soru (agent gerektiğinde sorar): **"Bu yeni fikir mevcut hedefe mi hizmet ediyor, yoksa Fikir Havuzu'na mı eklemeliyiz?"**

---

# BÖLÜM 2 — PROJE ÇERÇEVESİ

## 2.1 Doğru ad ve doğru zihniyet

Bu bir "dashboard" değil. Doğru adı: **Portföy Yönetimi Veri Platformu.** Dashboard bunun sadece görünen yüzü. Arkada olması gerekenler:

```
SAP Excel / LivaBudget Excel / Manuel Excel
        ↓
Import & Sync Layer  (kolon okuma, mapping, validation)
        ↓
Veri Temizleme / Normalize
        ↓
Firestore / SQL  (kalıcı veri)
        ↓
EVM Calculation Engine  (tek merkez)
        ↓
Dashboard  (görünen yüz)
        ↓
PDF / Excel / Yönetici Özeti
```

## 2.2 Katman Checklist (bitince var olmalı — sıralı şelale DEĞİL)

Bu bir zaman çizelgesi değildir. "Sistem bittiğinde bu katmanların hepsi var olmalı" listesidir. Her dikey dilim birkaç katmana dokunur. İlke: **temel önce.**

**Temel (ÖNCE — çekirdek değer):**
- [ ] Veri Modeli / Types
- [ ] Service Layer
- [ ] EVM Hesap Motoru ← sistemin taç mücevheri

**Giriş:**
- [ ] Excel Import + Kolon Mapping
- [ ] Validation
- [ ] Audit / Import batch

**Görünüm:**
- [ ] Dashboard (mevcut tasarım korunur)
- [ ] Rapor Export (PDF / Excel)

**Kurumsal:**
- [ ] Auth
- [ ] Rol / Proje bazlı yetki
- [ ] Firestore kalıcı veri

**ERTELENMİŞ (⏳/💡 — çekirdek kanıtlanınca, MVP'yi yavaşlatmaz):**
- [ ] SAP OData / LivaBudget API
- [ ] Otomasyon / n8n / scheduler
- [ ] AI Chat modülü (Aşama 11 — eval testiyle)
- [ ] Görsel geliştirme (Aşama 12 — ayrı loop)

> UX/UI, HTML/prototip, frontend, backend, veritabanı, auth, API, AI, entegrasyon, otomasyon, test, beta, production — bunlar TEMPORAL şelale aşaması DEĞİL, yukarıdaki katmanların içine dağılmış işlerdir. Agent bunları "önce hepsini UI, sonra hepsini backend" diye anlamaz.

## 2.3 Sürüm Roadmap

```
v1.0  Veri modeli + EVM motoru + EVM formül doğrulaması   ← EVM EN BAŞTA
v1.1  Excel giriş (import + mapping + validation)
v1.2  Dashboard veri bağlantısı
v1.3  PDF / Excel çıktı
v1.4  Staging deploy (mock/Excel — PRODUCTION DEĞİL)
v1.5  Auth + rol / proje yetkisi
v1.6  Firestore kalıcı veri     ← gerçek "canlıya alma" burada başlar
v1.7  SAP / LivaBudget Excel import olgunlaşması
v1.8  Kur / emtia / enflasyon takibi
v2.0  API / OData / otomasyon
v2.1  ERTELENMİŞ: AI Chat + eval
v2.2  ERTELENMİŞ: Görsel pass
```

> **Kritik ayrım:** v1.4 "staging deploy" = mock veri + auth yok. Bu bir DEMO/STAGING yayınıdır, **PRODUCTION değil.** Gerçek production auth + Firestore sonrası (v1.5–v1.6).

## 2.4 İlk Teknik Karar Matrisi

| Karar | İlk Sürüm Kararı |
|-------|------------------|
| Kullanıcı yapısı | Çok kullanıcılı |
| Auth | Firebase Auth iskeleti |
| Yetki | Rol + proje bazlı |
| Ana veri kaynağı | İlk etap Excel / SAP export |
| İleri veri kaynağı | SAP API/OData, LivaBudget entegrasyonu |
| Finansal veri | SQL Connect hazırlıklı |
| Operasyonel veri | Firestore hazırlıklı |
| Dış veri | Kur / emtia / enflasyon servis iskeleti |
| EVM hesapları | Merkezi calculation engine |
| Rapor dönemi | Veri giriş tarihinden AYRI |
| Dashboard | Portföy + proje + Alt PYP |
| Rapor | PDF / Excel buton iskeleti |
| Audit | Zorunlu |

## 2.5 Roller

`admin` · `yonetici` · `teknikOfis` · `finans` · `satinAlma` · `saha` · `sadeceOkuma`

---

# BÖLÜM 3 — EVM DOĞRULUK KURALLARI (EĞİTİM + İHLAL EDİLEMEZ KURAL)

Bu sistemin gerçek değeri EVM'nin **doğru** hesaplanmasıdır. Yanlış EVM = yönetim kuruluna yanlış tahmin = felaket. Bu kurallar her aşamada geçerlidir.

## 3.1 Temel formüller

```
PV  = Bu döneme kadar planlanan bütçe (Planned Value)
EV  = BAC × (fiziksel ilerleme %)          (Earned Value)
AC  = Gerçekleşen maliyet (SAP'den)         (Actual Cost)
BAC = Planlanan toplam bütçe                (Budget at Completion)

CV  = EV - AC        (Cost Variance)
SV  = EV - PV        (Schedule Variance)
CPI = EV / AC        (Cost Performance Index)
SPI = EV / PV        (Schedule Performance Index)
```

## 3.2 Zorunlu Koruma Kuralları

1. **AC, EV'den TÜRETİLMEZ.** AC = SAP'den gelen gerçek gerçekleşen maliyet, tamamen ayrı alan. (En sık ve en ölümcül EVM bug'ı budur.)
2. **BAC ≠ PV.** Planlanan toplam bütçe ile bu döneme kadar planlanan bütçe ayrı tutulur.
3. **Rapor dönemi ≠ veri giriş tarihi.** İkisi ayrı alan. (Haziran 2026 raporunu Temmuz'da girersin — sistem ikisini de saklar.)
4. **EV AGGREGATION — BAC-ağırlıklı.** Üst seviyeye (Alt PYP → Ana PYP → proje → portföy) fiziksel ilerleme **BAC ile ağırlıklandırılarak** toplanır. DÜZ ORTALAMA ALINMAZ.
   ```
   Portföy EV = Σ(BAC_i × ilerleme%_i)   — her kalem için ayrı, sonra topla
   YANLIŞ: ortalama(ilerleme%)            — bu roll-up'ı bozar
   ```
5. **EAC METODU — [KULLANICI KARARI, ONAY BEKLİYOR]:**
   - **Varsayılan (seçili):** `EAC = BAC / CPI` — mevcut maliyet performansı sonuna kadar devam eder varsayımı; inşaatta konservatif ve savunulabilir.
   - **Alternatif:** `EAC = AC + (BAC − EV) / (CPI × SPI)` — hem maliyet hem takvim sapıyorsa.
   - Agent bu satır kesinleşene kadar varsayılanı kullanır ve her rapora "EAC metodu: BAC/CPI (onay bekliyor)" notu düşer.
6. **Türev metrikler** seçilen EAC'ye bağlıdır:
   ```
   ETC  = EAC - AC        (Estimate to Complete)
   VAC  = BAC - EAC       (Variance at Completion)
   TCPI = (BAC - EV) / (BAC - AC)   (To-Complete Performance Index)
   ```
7. **Fiziksel ilerleme yoksa:** EV, CPI, SPI = "N/A". Sistem hata VERMEZ, "Physical progress missing" notu düşer.
8. **Tüm EVM hesapları `evmService.ts` içinde.** Component içinde EVM hesabı YAPILMAZ. Import sadece veriyi getirir, normalize eder, motoru çağırır. Dashboard hazır sonucu gösterir.

---

# AŞAMA 1 — KOD SAĞLIK KONTROLÜ

## Amaç (eğitim)
Yeni özellik eklemeden önce mevcut durumu anlamak. Soru: **"Bu dashboard gerçekten kurumsal uygulama iskeleti mi, yoksa sadece görsel ekran mı?"** Kod yazılmaz — sadece teşhis.

## Antigravity'ye Verilecek Komut (aynen ver)

```
Mevcut bütçe / EVM / portföy dashboard projesini sadece görsel olarak değil,
kurumsal uygulama iskeleti açısından incele.

Amacımız yeni özellik eklemek değil; önce kodun sağlığını, eksiklerini ve
kurumsal canlı kullanıma hazır olup olmadığını tespit etmek.

Aşağıdaki başlıklarda detaylı analiz yap:

1. Proje yapısı
- Hangi framework? (React / Vite / Next.js / başka?)
- Dosya yapısı düzenli mi?
- Component mimarisi var mı?
- Tekrarlayan kodlar var mı?

2. Routing ve ekranlar
- Kaç ekran var?
- Dashboard, veri girişi, bütçe, EVM, sapma, portföy, rapor ekranları ayrı mı?
- Yoksa hepsi tek sayfaya mı yığılmış?

3. Veri yapısı
- Veriler mock mı, gerçek kaynak var mı?
- Veriler component içinde mi tutuluyor?
- Merkezi data/service layer var mı?
- Planlanan, gerçekleşen, fiziksel ilerleme, PV, EV, AC, CPI, SPI, EAC alanları doğru ayrılmış mı?

4. Formül kontrolü
- EVM formülleri nerede hesaplanıyor?
- PV, EV, AC, CV, SV, CPI, SPI, BAC, EAC, ETC, VAC, TCPI doğru mu?
- Planlanan toplam bütçe ile bu aya kadar planlanan ayrılmış mı?
- Gerçekleşen maliyet ile fiziksel ilerleme karıştırılmış mı?
- Rapor dönemi ile veri giriş tarihi ayrılmış mı?

5. Dashboard mantığı
- Portföy toplamları doğru mu?
- Proje bazlı toplamlar doğru mu?
- Ana PYP / Alt PYP kırılımı korunuyor mu?
- Aylık ve kümülatif hesaplar ayrılmış mı?
- S-eğrisi planlanan / gerçekleşen / kazanılmış değer mantığıyla mı?

6. Kurumsal iskelet
- auth, rol bazlı yetki, service layer, type/interface, hata yönetimi,
  loading/empty state, audit log iskeleti, environment değişkenleri var mı?

7. Entegrasyona hazırlık
- Excel upload altyapısı, SAP importService, LivaBudget import mantığı,
  Firebase bağlantı dosyası, SQL/Firestore ayrımı, API endpoint iskeletleri var mı?

8. Test ve build
- npm install gerekiyor mu? npm run build çalışıyor mu?
- TypeScript / lint / console hatası var mı? Eksik import var mı?

9. Çok kullanıcılı kurumsal yapı
- Sistem tek kullanıcı gibi mi tasarlanmış?
- Kullanıcı modeli, roller, departman/proje bazlı yetki var mı?
- Aynı anda çok kullanıcı veri girişi, veri kilitleme/çakışma kontrolü,
  kim hangi projeyi görebilir mantığı var mı?

10. Otomatik veri çekme altyapısı
- SAP/LivaBudget verisi manuel mi, otomatik çekime hazır mı?
- SAP/LivaBudget Excel import edilebilir mi?
- importService / syncService / scheduler var mı?
- Veri kaynağı alanı (SAP/LivaBudget/Excel/Manual) tutuluyor mu?
- Duplicate kontrolü var mı?

11. Canlı dış veri takip altyapısı
- Kur (EUR/TL, USD/TL, EUR/USD), enflasyon (TÜFE/ÜFE/inşaat maliyet endeksi),
  emtia (demir, çelik, alüminyum, bakır, akaryakıt) için servis iskeleti var mı?
- Bu veriler dönemsel saklanıyor mu? Bütçe etkisi olarak kullanılabilir mi?

12. Zamanlanmış görev / otomasyon
- Günlük kur, aylık enflasyon, haftalık SAP/LivaBudget senkron mantığı var mı?
- Veri çekme başarı/başarısız loglanıyor mu? Hatalı veride uyarı var mı?

ÇIKTI FORMATIN:
A) Mevcut durum özeti
B) Kritik hatalar
C) Eksik kurumsal parçalar
D) Yanlış veya riskli formüller
E) Hemen düzeltilmesi gerekenler
F) Sonra yapılacaklar
G) Sakın dokunma / bozulmaması gereken alanlar
H) Önerilen sıradaki işlem

ÖNEMLİ:
- Şu aşamada kod yazma. Dosya silme. Görsel tasarımı bozma. Yeni modül ekleme.
- Sadece analiz yap.
- Sonunda "Bir sonraki adım için onay bekliyorum" yaz.
```

## Beklenen Cevap
Agent şunu söylemeli: Şu çalışıyor · şu eksik · şu yanlış · şu mock · şu gerçek entegrasyona hazır değil · önce şunu düzeltmeliyiz.

## Kontrol Tablosu (agent cevabında bunları ara)

| Soru | Neden önemli? |
|------|---------------|
| Veri component içinde mi? | Sonra Firebase/SQL bağlanamaz |
| Formüller tek yerde mi? | Hatalı EVM üretir |
| Rapor dönemi ayrı mı? | Yanlış ay raporu çıkar |
| Toplam bütçe / dönemsel plan ayrı mı? | Sapma analizi bozulur |
| AC gerçek veri mi, türetilmiş mi? | EVM tamamen bozulur |
| EAC formülü doğru mu? | Tahmini nihai bütçe yanlış çıkar |
| Build geçiyor mu? | Canlıya alınamaz |
| Service layer var mı? | Kurumsal mimari kurulamaz |

## Definition of Done
Analiz raporu A–H formatında üretildi · KOD YAZILMADI · "onay bekliyorum" yazıldı.

## STOP
Bu aşamada kod yazmak, dosya silmek, tasarım değiştirmek yasak. Sadece rapor.

---

# AŞAMA 2 — SİSTEM ANALİSTİ / KARAR SORULARI

## Amaç (eğitim)
Kod yazmadan önce agent'ı sistem analisti gibi düşündürmek. Eksik kararları soru haline getirmek. Sen her soruya cevap vermek zorunda değilsin — cevapları 3 kategoriye ayıracağız.

## Antigravity'ye Verilecek Komut (aynen ver)

```
Bu proje tek kullanıcılı dashboard değil.

Bu proje:
- çok kullanıcılı,
- rol bazlı yetkili,
- SAP / LivaBudget / Excel'den veri alabilecek,
- kur / emtia / enflasyon verilerini takip edebilecek,
- EVM ve sapma analizi yapacak,
- portföy yönetimi sağlayacak,
- ileride Firebase / SQL / otomasyon katmanına bağlanacak
kurumsal bütçe ve portföy yönetimi modülüdür.

Kod yazmaya başlamadan önce bana aşağıdaki başlıklarda eksik karar sorularını sor:

1. Kullanıcı ve rol yapısı
2. Proje bazlı yetki yapısı
3. Veri kaynakları
4. SAP veri alma yöntemi
5. LivaBudget veri alma yöntemi
6. Excel import formatı
7. Rapor dönemi mantığı
8. Planlanan / gerçekleşen / fiziksel ilerleme ayrımı
9. EVM hesap mantığı
10. Tahmini nihai bütçe (EAC) hesap mantığı
11. Kur verisi kaynağı
12. Enflasyon verisi kaynağı
13. Emtia verisi kaynağı
14. Veri güncelleme sıklığı
15. Duplicate veri kontrolü
16. Audit trail
17. Dashboard ekranları
18. Rapor çıktıları
19. Bildirim / uyarı sistemi
20. Canlıya alma gereksinimleri

Her başlık altında:
- Bana sorulması gereken net soruları yaz.
- Her soru için neden önemli olduğunu açıkla.
- Cevap verilmezse hangi varsayımla ilerlenebileceğini belirt.
- Cevap verilmeden kodlanırsa doğacak riski yaz.

Şu aşamada kod yazma. Sadece karar soruları üret.
Sonunda "Bu sorular cevaplanmadan kodlamaya geçilmemeli" yaz.
```

## Cevapları Kategorize Etme (sen yaparsın)

**🔥 Şimdi cevaplanacaklar** (kod mimarisini etkiler): kullanıcı rolleri · veri kaynakları · rapor dönemi · EVM formülleri · veri modeli · SAP/LivaBudget import mantığı · SQL mi Firestore mu.

**⏳ Sonra** (canlıdan önce netleşir): otomatik SAP API · Teams bildirimi · PDF rapor formatı · emtia kaynakları · enflasyon detayları.

**💡 Fikir Havuzu** (ilk modülü yavaşlatmasın): AI yönetici yorumu · mobil uygulama · Monte Carlo · risk modülü · hedge · satın alma öneri motoru.

## Definition of Done
20 başlıkta karar soruları + her biri için (neden / varsayım / risk) üretildi.

## STOP
Kod yazma. Sadece soru.

---

# AŞAMA 3 — VERİ MODELİ + SERVICE + EVM MOTORU

## Amaç (eğitim)
Çekirdeğin temeli. **Data-first:** önce type/config, sonra service iskeleti, sonra EVM motoru. Bu aşama **üç alt-adıma bölünür** — her alt-adım ayrı loop, ayrı TAMAM.

## Hedef Klasör Yapısı

```
src/
├─ app veya pages/
├─ components/
│  ├─ layout/  ├─ dashboard/  ├─ charts/  ├─ tables/  ├─ forms/  └─ upload/
├─ services/
│  ├─ sapService.ts        ├─ livaBudgetService.ts   ├─ excelImportService.ts
│  ├─ exchangeRateService.ts ├─ inflationService.ts  ├─ commodityService.ts
│  ├─ syncService.ts       ├─ evmService.ts          ├─ budgetService.ts
│  ├─ portfolioService.ts  ├─ reportService.ts       ├─ auditService.ts
│  └─ notificationService.ts
├─ lib/
│  ├─ firebase.ts  ├─ sql.ts  ├─ auth.ts  └─ apiClient.ts
├─ types/
│  ├─ user.ts  ├─ project.ts  ├─ budget.ts  ├─ evm.ts
│  ├─ import.ts  ├─ marketData.ts  ├─ report.ts  └─ audit.ts
├─ utils/
│  ├─ date.ts  ├─ currency.ts  ├─ validation.ts  └─ formulas.ts
└─ config/
   ├─ roles.ts  ├─ permissions.ts  ├─ dataSources.ts  └─ dashboardConfig.ts
```

## ADIM 3a — Types + Config (aynen ver)

```
Mevcut projede zaten dosyalar var. Sıfırdan proje kurma.

Görevin: SADECE eksik type ve config dosyalarını ekle. Var olanı üzerine YAZMA;
varsa raporla ve sor.

Önce oku: package.json, README.md, AGENTS.md, TASKS.md, CHANGELOG.md,
src/ klasörü, docs/ klasörü, vite.config.ts, index.html.

Type dosyaları (yoksa oluştur):
- src/types/user.ts, project.ts, budget.ts, evm.ts, import.ts, marketData.ts,
  report.ts, audit.ts

Minimum alanlar:
- user role
- project, portfolio
- budget item
- planned / actual / physical progress
- PV / EV / AC
- CPI / SPI / EAC / ETC / VAC / TCPI
- reportPeriod, dataEntryDate
- sourceSystem, importBatchId
- audit log

Config dosyaları (yoksa oluştur):
- src/config/roles.ts, permissions.ts, dataSources.ts, dashboardConfig.ts
Roller: admin, yonetici, teknikOfis, finans, satinAlma, saha, sadeceOkuma

KURALLAR:
- Var olan dosyayı üzerine yazma.
- Mevcut dashboard tasarımını bozma.
- Kod build hatasız olsun.
- Sonunda: eklenen dosyalar, dokunulmayanlar, build sonucu → raporla, onay bekle.
```

## ADIM 3b — Service İskeletleri (aynen ver)

```
Sadece eksik service dosyalarını ekle. Gerçek API'ye bağlanmak zorunda değil.
Fonksiyon isimleri, type bağlantıları, mock fallback ve TODO açıklamaları hazır olsun.

Service dosyaları (yoksa oluştur):
- sapService.ts, livaBudgetService.ts, excelImportService.ts,
  exchangeRateService.ts, inflationService.ts, commodityService.ts,
  syncService.ts, evmService.ts, budgetService.ts, portfolioService.ts,
  reportService.ts, auditService.ts, notificationService.ts

Lib dosyaları (yoksa oluştur):
- firebase.ts, sql.ts, auth.ts, apiClient.ts
Kurallar: gizli key yazma; .env üzerinden okunacak yapı kur; gerçek bağlantı
yoksa mock/placeholder bırak; public (VITE_*) değişkenle secret karıştırma.

auditService + audit type alanları:
- userId, userName, role, actionType, module, recordId, oldValue, newValue,
  timestamp, sourceSystem, importBatchId

KURALLAR: var olanı üzerine yazma · build hatasız · raporla · onay bekle.
```

## ADIM 3c — EVM Motoru (aynen ver)

```
src/services/evmService.ts içinde EVM hesaplarını MERKEZİ fonksiyonlar olarak kur.
BÖLÜM 3'teki EVM Doğruluk Kuralları'na harfiyen uy.

Hesaplar:
- CV = EV - AC
- SV = EV - PV
- CPI = EV / AC
- SPI = EV / PV
- EV  = BAC × fiziksel ilerleme %
- EAC = BAC / CPI            (varsayılan; "onay bekliyor" notu düş)
- ETC = EAC - AC
- VAC = BAC - EAC
- TCPI = (BAC - EV) / (BAC - AC)

ZORUNLU:
- AC, EV'den TÜRETİLMESİN. AC ayrı gerçekleşen maliyet.
- BAC ile PV ayrı.
- Rapor dönemi ile veri giriş tarihi ayrı.
- Üst seviye EV = Σ(BAC_i × ilerleme%_i); düz ortalama ALMA.
- Fiziksel ilerleme yoksa EV/CPI/SPI = "N/A", hata verme.
- EVM hesabı component içinde YAPILMASIN — sadece bu serviste.

Sonunda: EVM formül kontrol sonucu + build sonucu → raporla, onay bekle.
```

## Definition of Done (Aşama 3 tümü)
Types + config + service iskeletleri + evmService mevcut · `npm run build` hatasız · 0 TS error · mevcut dashboard hâlâ açılıyor.

## STOP
Her alt-adım sonunda dur, TAMAM bekle. Üç adımı tek seferde yapma.

---

# AŞAMA 4 — EXCEL IMPORT + MAPPING + VALIDATION

## Amaç (eğitim)
İlk veri kaynağı Excel. Sistem SAP export, LivaBudget export ve manuel Excel'i okuyacak; kolon sırasına GÜVENMEYECEK, başlık adına göre eşleştirecek; kullanıcıya mapping + preview gösterecek; onaydan sonra veriyi işleyecek.

## Import Akışı

```
1. Kullanıcı Excel yükler
2. Sistem dosya adı, sheet adları, kolon başlıkları, ilk 20 satırı okur
3. Dosya tipini tahmin eder (SAP / LivaBudget / Manual Budget / Actual / Physical / Cash Flow)
4. Kolonları hedef alanlarla eşleştirmeye çalışır
5. Kullanıcı mapping'i onaylar / düzeltir
6. Validation
7. Hatalı satır + eksik kolonları listeler
8. Preview tablosu
9. Kullanıcı "İçe Aktar"
10. Normalize
11. Import batch kaydı
12. Dashboard veri modeline işler
13. EVM / sapma tetikler
14. Audit log
15. Sonuç özeti
```

## Antigravity'ye Verilecek Komut (aynen ver)

```
Excel giriş motorunu kurumsal modül olarak kur, eksik dosyaları ekle.

GEREKLİ DOSYALAR (yoksa oluştur):
- src/components/upload/ExcelUploadPanel.tsx
- src/components/upload/ColumnMappingTable.tsx
- src/components/upload/ImportPreviewTable.tsx
- src/components/upload/ImportValidationPanel.tsx
- src/components/upload/ImportHistoryPanel.tsx
- src/services/excelImportService.ts
- src/services/sapImportService.ts
- src/services/livaBudgetImportService.ts
- src/services/importValidationService.ts
- src/services/importMappingService.ts
- src/services/importBatchService.ts
- src/types/import.ts, budget.ts, evm.ts

HEDEF VERİ ALANLARI (Excel bunlara eşlenebilir olmalı):
projectCode, projectName, anaPYP, altPYP, pypCode, budgetVersion, reportPeriod,
dataEntryDate, plannedBudgetTotal, plannedBudgetToDate, actualCost,
physicalProgressPercent, cashIn, cashOut, currency, exchangeRate, sourceSystem,
sourceFileName, importBatchId, importedBy, importedAt

DOSYA TİPİ ALGILAMA (muhtemel kolonlar):
- SAP: PYP, Masraf Yeri, Belge Tarihi, Kayıt Tarihi, Tutar, Para Birimi, Proje, Hesap, Satıcı
- LivaBudget: Proje Kodu, Bütçe Versiyonu, Ana PYP, Alt PYP, Planlanan Bütçe, Revize Bütçe, Dönem, Para Birimi
- Fiziksel ilerleme: Proje, PYP, İş Kalemi, Planlanan %, Gerçekleşen %, Rapor Dönemi
- Nakit akış: Proje, Ay, Planlanan Nakit, Gerçekleşen Nakit, Kümülatif Plan, Kümülatif Gerçekleşen

VALIDATION KURALLARI:
- projectCode boş olamaz
- reportPeriod boş olamaz
- actualCost negatif olamaz
- physicalProgressPercent 0-100 arası
- currency boşsa varsayılan EUR + UYARI
- Aynı projectCode + altPYP + reportPeriod + sourceSystem tekrar gelirse duplicate uyarısı
- Aynı dosya tekrar yüklenirse importBatch duplicate kontrolü
- plannedBudgetTotal ile plannedBudgetToDate ayrı tutulmalı
- actualCost EV'den türetilmemeli; Excel'den gelen gerçekleşen olarak tutulmalı

EVM TETİKLEME (import sonrası):
BAC = plannedBudgetTotal, PV = plannedBudgetToDate, AC = actualCost,
EV = BAC × physicalProgress%  → sonra CV, SV, CPI, SPI, EAC, ETC, VAC, TCPI
(hesaplar SADECE evmService içinde; import motoru veriyi getirir ve motoru çağırır)

KISITLAR:
- Mevcut dashboard tasarımını bozma · çalışan sayfaları silme · büyük refactor yapma
- Gerçek SAP API kurma · ilk aşamada Excel üzerinden çalış · build hatasız
- Var olan dosyayı üzerine yazma

ÇIKTI: A) oluşturulan/güncellenen dosyalar B) import akışı C) algılanan dosya tipleri
D) hedef alanlar E) validation kuralları F) EVM tetikleme G) mock kalan yerler
H) build sonucu → raporla, onay bekle.
```

## Kontrol (agent cevabında olmalı)
Excel upload ✅ · kolon mapping ✅ · validation ✅ · preview ✅ · import batch ✅ · duplicate kontrol ✅ · EVM service tetikleme ✅ · dashboard veri bağlantısı ✅.

## Definition of Done
Upload + mapping + validation + preview çalışıyor · duplicate kontrolü var · `build` hatasız · mevcut dashboard açılıyor.

---

# AŞAMA 5 — DASHBOARD VERİ BAĞLANTISI (Plan + Actual Birleşimi)

## Amaç (eğitim)
Kritik kural: **Planlanan = LivaBudget, Gerçekleşen = SAP.** İki farklı Excel formatı, farklı kolonlar. Sistem başlık okur, normalize eder, doğru hedefe eşler, sonra birleştirir.

## Antigravity'ye Verilecek Komut (aynen ver)

```
İlk aşamada API yok; veriler Excel'den gelecek. SAP ve LivaBudget formatları farklı.
Sistem sabit kolon sırasına güvenmeyecek — başlıkları okuyup normalize edecek.

KAYNAK AYRIMI:
1. LivaBudget Excel = PLANLANAN veri kaynağı → doldurur:
   projectCode, projectName, anaPYP, altPYP, pypCode, budgetVersion,
   reportPeriod, plannedBudgetTotal, plannedBudgetToDate, currency
2. SAP Excel = GERÇEKLEŞEN veri kaynağı → doldurur:
   projectCode, pypCode, anaPYP, altPYP, reportPeriod, actualCost,
   documentDate, postingDate, accountCode, vendor, currency

BAŞLIK NORMALİZASYONU:
- Sheet adlarını, kolon başlıklarını oku
- Büyük/küçük harf, Türkçe karakter, boşluk, tire, alt çizgi, nokta farklarını tolere et
- Kolon SIRASINA göre değil, başlık ADINA göre eşleştir
- Eşleşmeyen kolonları kullanıcıya göster; eksik zorunlu alanları uyar
- Kullanıcı onayından sonra içeri al

Örnek eşleşmeler:
- Proje Kodu → projectCode      - Proje → projectCode veya projectName
- Alt PYP → altPYP              - PYP Kodu → pypCode
- Bütçe Versiyonu → budgetVersion - Planlanan Bütçe → plannedBudgetTotal
- Bu Aya Kadar Planlanan → plannedBudgetToDate  - Dönem → reportPeriod
- Tutar / Gerçekleşen → actualCost - Belge Tarihi → documentDate
- Kayıt Tarihi → postingDate

HEDEF ORTAK MODELLER:
BudgetPlanRecord: projectCode, projectName, anaPYP, altPYP, pypCode,
  budgetVersion, reportPeriod, plannedBudgetTotal, plannedBudgetToDate,
  currency, sourceSystem=LivaBudget, sourceFileName, importedAt
ActualCostRecord: projectCode, pypCode, anaPYP, altPYP, reportPeriod,
  actualCost, documentDate, postingDate, accountCode, vendor, currency,
  sourceSystem=SAP, sourceFileName, importedAt

BİRLEŞİM MANTIĞI:
Anahtar: projectCode + (pypCode veya altPYP) + reportPeriod
Eşleşince hesapla:
  plannedBudgetTotal, plannedBudgetToDate, actualCost,
  variance = actualCost - plannedBudgetToDate,
  variancePercent = variance / plannedBudgetToDate,
  physicalProgressPercent (varsa ayrı tut),
  EV/PV/AC/CPI/SPI/EAC → bu birleşik modelden evmService ile hesaplanır

KISITLAR: mock/demo Excel ile test edilebilir olsun · gerçek API kurma ·
büyük refactor yapma · dashboardu bozma · build hatasız.

ÇIKTI: A) LivaBudget kolon→alan B) SAP kolon→alan C) zorunlu alanlar
D) eşleşme anahtarı E) eksik kolonda davranış F) dashboard hangi birleşik veriyi
kullanıyor G) build sonucu → raporla, onay bekle.
```

## Definition of Done
LivaBudget=planlanan, SAP=gerçekleşen birleşiyor · dashboard birleşik veriyi gösteriyor · `build` hatasız.

---

# AŞAMA 6 — PDF + EXCEL RAPOR EXPORT

## Amaç (eğitim)
Import edilen veriden yönetici raporu üretmek. Rapor verisi component içinden toplanmaz — `reportService` üzerinden normalize edilir, `pdfExportService` ve `excelExportService` üretir.

## Antigravity'ye Verilecek Komut (aynen ver)

```
PDF ve Excel rapor çıktısı iskeletini ekle.

GEREKLİ DOSYALAR (yoksa oluştur):
- src/components/reports/ReportExportPanel.tsx
- src/services/reportService.ts, pdfExportService.ts, excelExportService.ts
- src/types/report.ts

RAPOR EXPORT PANELİ butonları:
- PDF Rapor Oluştur · Excel Rapor Oluştur · Portföy Raporu · Proje Bazlı Rapor · Alt PYP Detay Raporu

PDF RAPOR minimum bölümler:
Rapor başlığı, rapor dönemi, veri giriş tarihi, son güncelleme tarihi,
portföy özeti, proje bazlı bütçe durumu, planlanan/gerçekleşen karşılaştırması,
sapma analizi, EVM özet metrikleri, tahmini nihai bütçe/EAC,
Alt PYP kritik sapma tablosu, import log özeti, varsayımlar/notlar

EXCEL RAPOR minimum sheetler:
1. Portfolio Summary   (totalPlannedBudget, totalPlannedToDate, totalActualCost,
                        totalVariance, totalVariancePercent, reportPeriod)
2. Project Summary     (projectCode, projectName, plannedBudgetTotal,
                        plannedBudgetToDate, actualCost, variance, variancePercent)
3. Budget Detail       (projectCode, anaPYP, altPYP, pypCode, plannedBudgetTotal,
                        plannedBudgetToDate, actualCost, currency, sourceSystem)
4. Variance Analysis   (projectCode, pypCode, plannedBudgetToDate, actualCost,
                        variance, variancePercent, status)
5. EVM Analysis        (projectCode, pypCode, BAC, PV, AC, physicalProgressPercent,
                        EV, CPI, SPI, EAC, note)
6. Physical Progress
7. Import Logs         (sourceSystem, sourceFileName, importedAt, rowCount,
                        mappedColumnCount, errorCount)
8. Assumptions         (reportPeriodRule, dataEntryDateRule,
                        plannedDataSource=LivaBudget, actualDataSource=SAP,
                        currencyRule, evmRule=BAC/CPI onay bekliyor)

DOSYA ADI FORMATI:
- Folkart_Budget_Portfolio_Report_[reportPeriod]_[date].pdf
- Folkart_Budget_Portfolio_Report_[reportPeriod]_[date].xlsx

EVM UYARISI: fiziksel ilerleme yoksa EV/CPI/SPI = "N/A", hata verme,
"Physical progress missing" notu düş.

KÜTÜPHANE: PDF için jsPDF veya pdfmake; Excel için xlsx. Önce mevcut yapıyı
kontrol et, gerekirse package.json'a ekle.

RAPOR ÜRETİM MANTIĞI: veri component'ten direkt toplanmasın; reportService
normalize etsin; eksik veri varsa rapor yine oluşsun ama "Eksik veri/varsayım"
alanına yazsın.

KISITLAR: dashboardu bozma · import akışını bozma · gerçek API kurma · sadece
export altyapısı · build hatasız.

ÇIKTI: A) eklenen/güncellenen dosyalar B) PDF bölümleri C) Excel sheet listesi
D) veri kaynağı E) eksik veri davranışı F) kütüphaneler G) build sonucu
H) mock kalan alanlar → raporla.
SONUNDA: "PDF ve Excel çıktı iskeleti tamamlandı, onay bekliyorum." yaz.
```

## Definition of Done
PDF + Excel export butonları çalışıyor · reportService üzerinden besleniyor · `build` hatasız.

---

# AŞAMA 7 — STAGING DEPLOY (Firebase Hosting, mock veri)

## Amaç (eğitim)
Mevcut çalışan sistemi **bozmadan** demo yayınına almak. Bu PRODUCTION DEĞİL — mock/Excel veri, auth yok. Amaç: paydaşlara gösterilebilir bir staging.

## Antigravity'ye Verilecek Komut (aynen ver)

```
Projeyi STAGING (demo) yayınına hazırla. Bu production değil; mock/Excel veriyle çalışır.

YAPILACAKLAR:
1. Build kontrolü: package.json scriptleri, npm run build, TS hataları, eksik
   importlar düzelt, dist oluşuyor mu.
2. Firebase dosyaları (eksikse oluştur): firebase.json, .firebaserc, .env.example
   firebase.json: public = "dist" (Vite), SPA rewrite ("**" → "/index.html").
3. Environment: gerçek secret yazma; Firebase config .env.example'da placeholder;
   API/SMTP/SAP/LivaBudget key hardcoded olmasın; .env.local .gitignore'da olsun.
   .env.example minimum:
   VITE_FIREBASE_API_KEY=, VITE_FIREBASE_AUTH_DOMAIN=, VITE_FIREBASE_PROJECT_ID=,
   VITE_FIREBASE_STORAGE_BUCKET=, VITE_FIREBASE_MESSAGING_SENDER_ID=, VITE_FIREBASE_APP_ID=
4. README: canlıya alma adımları (npm install, dev, build, firebase login,
   firebase init hosting, firebase use [id], firebase deploy). Belirt: ilk sürüm
   Excel/manual/mock veriyle çalışır; SAP/LivaBudget API sonraki sürüm; planlanan
   veri LivaBudget Excel'den, gerçekleşen SAP Excel'den.
5. CHANGELOG.md v1.4 staging: dashboard, Excel import, SAP/LivaBudget mapping,
   PDF/Excel export, Firebase Hosting hazırlığı.
6. docs/deployment-checklist.md (yoksa): build başarılı mı · dist oluştu mu ·
   firebase.json doğru mu · .env.example var mı · secret hardcoded yok mu ·
   dashboard açılıyor mu · import ekranı açılıyor mu · export butonları görünüyor mu ·
   mock kalan alanlar listelendi mi · firebase project id doğru mu.
7. Smoke test: dashboard açılıyor mu · import ekranı açılıyor mu · LivaBudget/SAP
   import seçenekleri var mı · rapor export paneli görünüyor mu · hata ekranı düzgün mü.

KISITLAR: yeni büyük özellik ekleme · dashboardu bozma · import/export akışını
bozma · SAP API / n8n / SQL Connect ekleme · sadece staging hazırlığı · build hatasız.

ÖNEMLİ: firebase deploy komutunu KENDİN ÇALIŞTIRMA. Önce bana rapor ver.

ÇIKTI: A) build B) firebase dosyaları C) env D) README deploy adımları
E) checklist F) smoke test G) canlıya alma komutu H) canlı sonrası ilk öneri.
SONUNDA: "Staging deploy hazırlığı tamamlandı, deploy için onay bekliyorum." yaz.
```

## Definition of Done
`firebase.json` + `.env.example` doğru · `dist` oluşuyor · smoke test geçti · deploy komutu HAZIR (agent kendisi deploy ETMEDİ).

## STOP
Agent `firebase deploy`'u çalıştırmaz. Sen çıktıyı görüp karar verirsin: (1) deploy et (2) önce build düzelt (3) önce import düzelt (4) önce env/firebase düzelt.

---

# AŞAMA 8 — AUTH + ROL + YETKİ (kurumsal güvenlik)

## Amaç (eğitim)
Staging demo'dan gerçek production'a geçişin ilk yarısı. Kim giriş yapabilir, kim hangi projeyi görür.

## Antigravity'ye Verilecek Komut (aynen ver)

```
Firebase Auth + rol/proje bazlı yetkiyi kur. Mevcut çalışan yapıyı bozma.

YAPILACAKLAR:
1. Firebase Auth entegrasyonu (login/logout, oturum durumu). Gerçek secret yazma;
   config .env'den.
2. Roller: admin, yonetici, teknikOfis, finans, satinAlma, saha, sadeceOkuma
   - Rol Firebase Custom Claims ile taşınacak (client'ta hardcode edilmeyecek).
   - Rol atama fonksiyonu server tarafında (Cloud Function iskeleti; TODO bırak).
3. Proje bazlı yetki: kullanıcı → görebileceği projeler eşlemesi.
   - Firestore Security Rules iskeleti (kim hangi projeyi okur/yazar).
4. Menü/ekran yetkileri: rol bazlı menü görünürlüğü.
5. sadeceOkuma rolü: veri girişi/düzenleme butonları gizli/pasif.

KISITLAR: dashboardu bozma · mevcut ekranları kırma · gerçek key yazma ·
build hatasız.

ÇIKTI: A) auth akışı B) rol yapısı C) custom claims mantığı D) proje yetki
mantığı E) security rules iskeleti F) menü yetkileri G) build sonucu → raporla,
onay bekle.
```

## Definition of Done
Login/logout çalışıyor · roller custom claims'te · proje bazlı yetki iskeleti + security rules mevcut · `build` hatasız.

---

# AŞAMA 9 — FIRESTORE KALICI VERİ (gerçek veri katmanı)

## Amaç (eğitim)
Mock veriden gerçek kalıcı veriye geçiş. Import edilen veri Firestore'a yazılır, dashboard oradan okur. **Gerçek "canlıya alma" burada anlam kazanır.**

## Antigravity'ye Verilecek Komut (aynen ver)

```
Import edilen veriyi Firestore'a kalıcı yaz; dashboard Firestore'dan okusun.
Mevcut mock akışı bozmadan, gerçek veri katmanını devreye al.

YAPILACAKLAR:
1. Firestore koleksiyon şeması: projects, budgetPlanRecords, actualCostRecords,
   evmResults, importBatches, auditLogs, users, marketData
2. budgetService / portfolioService / auditService → Firestore okuma/yazma.
3. Import sonrası: BudgetPlanRecord ve ActualCostRecord Firestore'a yazılsın;
   importBatch + auditLog kaydı oluşsun.
4. Duplicate/batch: aynı projectCode+altPYP+reportPeriod+sourceSystem varsa
   GÜNCELLE (yeni kayıt açma); aynı dosya tekrar gelirse importBatch duplicate uyar.
5. Dashboard: mock yerine Firestore'dan okur; boş veride empty state gösterir.
6. Firestore Security Rules'u Aşama 8 yetkisiyle bağla (proje bazlı okuma).
7. NOT: analitik/ağır sorgu (dönemsel/çapraz sapma) ileride BigQuery'ye taşınabilir;
   şimdilik Firestore + client hesap yeterli, TODO bırak.

KISITLAR: dashboardu bozma · mock fallback'i tamamen silme (offline/demo için kalsın,
flag ile) · gerçek key yazma · build hatasız.

ÇIKTI: A) koleksiyon şeması B) yazma akışı C) duplicate/update mantığı
D) dashboard okuma E) security rules bağlantısı F) build sonucu → raporla, onay bekle.
```

## Definition of Done
Import → Firestore yazıyor · dashboard Firestore'dan okuyor · duplicate/update çalışıyor · security rules bağlı · `build` hatasız.

---

# AŞAMA 10 — PRODUCTION DEPLOY (gerçek canlıya alma)

## Amaç (eğitim)
Auth + kalıcı veri hazır. Artık gerçek production yayını. Güvenlik ve son kontroller.

## Antigravity'ye Verilecek Komut (aynen ver)

```
Projeyi PRODUCTION yayınına hazırla. Auth ve Firestore aktif.

YAPILACAKLAR:
1. Production build kontrolü (npm run build, dist, 0 hata).
2. Firestore Security Rules PRODUCTION seviyesi: varsayılan deny; sadece yetkili
   rol/proje okuma-yazma. Rules'u gözden geçir ve sıkılaştır.
3. Environment: production .env değerleri ayrı; secret'lar hosting environment'ta,
   repoda değil.
4. Auth: yalnız yetkili kullanıcı (Folkart hesabı) girişi; kayıt açık bırakılmaz.
5. CHANGELOG.md v1.6 production notu.
6. docs/production-checklist.md: rules deny-by-default mı · auth zorunlu mu ·
   secret repoda yok mu · audit log yazılıyor mu · yedek/rollback planı var mı ·
   hata izleme (console/monitoring) var mı.
7. Smoke test (production build): login zorunlu mu · yetkisiz kullanıcı erişemiyor mu ·
   veri Firestore'dan geliyor mu · export çalışıyor mu.

KISITLAR: yeni özellik ekleme · dashboardu bozma · deploy'u KENDİN çalıştırma.

ÇIKTI: A) build B) rules production durumu C) env/secret D) auth politikası
E) checklist F) smoke test G) deploy komutu H) rollback planı.
SONUNDA: "Production deploy hazırlığı tamamlandı, onay bekliyorum." yaz.
```

## Definition of Done
Rules deny-by-default · auth zorunlu · secret repoda yok · smoke test geçti · deploy komutu + rollback planı hazır.

---

# AŞAMA 11 — (ERTELENMİŞ) AI CHAT + EVAL TESTİ

## Amaç (eğitim)
Proje verisi üzerinden soru-cevap. **Ama:** bütçe/EVM verisinde yanlış sayı söyleyen bir chat = felaket. Bu yüzden chat ile birlikte **eval testi ZORUNLU.** Eval = bilinen doğru cevaplı soru seti; chat o sete karşı test edilir; sapma ölçülür. Eval geçmeden chat canlıya ALINMAZ.

## Antigravity'ye Verilecek Komut (aynen ver)

```
Firebase üzerinden AI chat modülü kur (Firebase AI Logic / Gemini). Proje verisi
üzerinden soru-cevap. Bu ERTELENMİŞ bir aşamadır; çekirdek bozulmayacak.

YAPILACAKLAR:
1. chatService: kullanıcı sorusu + ilgili proje/EVM verisi (context) → Gemini → cevap.
   - Sayısal cevaplar UYDURULMAYACAK; sadece verilen context'ten türetilecek.
   - Context'te olmayan bilgi sorulursa "veri yok" desin, tahmin etmesin.
2. Chat UI paneli (mevcut dashboard bozulmadan, ayrı ekran/panel).
3. EVAL TESTİ (zorunlu):
   - docs/chat-eval.md içinde bilinen doğru cevaplı soru seti hazırla
     (ör: "Terra projesi Haziran 2026 CPI kaç?" → beklenen: hesaplanmış doğru değer).
   - Chat'i bu sete karşı çalıştır; her soruda cevap vs beklenen karşılaştır.
   - Sapma/yanlış cevap sayısını raporla.
   - Kural: eval'de sayısal hata varsa chat CANLIYA ALINMAZ; context/prompt düzeltilir.
4. Gerçek key .env'den; hardcode yok.

KISITLAR: çekirdek EVM/dashboard/import bozulmayacak · build hatasız.

ÇIKTI: A) chat akışı B) context nasıl besleniyor C) eval soru seti
D) eval sonucu (kaç doğru/yanlış) E) canlıya alınabilir mi F) build sonucu → raporla.
```

## Definition of Done
Chat çalışıyor · eval soru seti hazır · eval çalıştırıldı · sapma raporlandı · sayısal hata yoksa "alınabilir" denildi.

---

# AŞAMA 12 — (ERTELENMİŞ, AYRI LOOP) GÖRSEL PASS

## Amaç (eğitim)
Görsellik = frontend/tasarım işi (API DEĞİL). Bu iskelet loop'una KARIŞTIRILMAZ — çünkü "bozma" ile "geliştir" aynı loop'ta çelişir. İskelet çalışır durumdayken, AYRI bir loop ve tasarım referansıyla yapılır.

## Antigravity'ye Verilecek Komut (aynen ver — ayrı loop, iskelet stabilken)

```
Görsel iyileştirme pass'i. İşlevi bozmadan sadece görünüm/deneyim iyileştir.

YAPILACAKLAR:
1. Tasarım token sistemi: renk paleti, spacing ölçeği, tipografi ölçeği, radius,
   gölge — tek config dosyasında (tema tutarlılığı).
2. ECharts tema config: tüm grafikler (S-eğrisi, sapma, waterfall, ısı haritası)
   ortak temaya bağlansın; renkler token'lardan.
3. Layout: dashboard grid hizası, boşluk tutarlılığı, kart/tablo görünümü,
   loading ve empty state görselleri.
4. Responsive: desktop öncelik (1920×1080, 2560×1440), sonra tablet/mobil.
5. Erişilebilirlik: kontrast, font okunabilirliği.

KISITLAR: hiçbir hesap/veri/işlev DEĞİŞMEYECEK · sadece görünüm · build hatasız ·
her değişiklik sonrası ilgili ekranın hâlâ çalıştığını doğrula.

ÇIKTI: A) token sistemi B) ECharts tema C) layout değişiklikleri D) responsive
durumu E) önce/sonra ekran F) build sonucu → raporla.
```

## Definition of Done
Token sistemi + ECharts tema + layout tutarlı · işlev değişmedi · responsive · `build` hatasız.

---

# EK A — GÜVENLİK & SECRET KURALLARI (her aşamada geçerli)

- Gerçek key/secret ASLA repoda yazılmaz.
- Tüm config `.env`; `.env.example` placeholder içerir; `.env.local` `.gitignore`'da.
- Public (VITE_*) değişkenle secret karıştırılmaz.
- Production'da secret hosting environment'ta tutulur.

# EK B — TEK AGENT KONVANSİYONU

Bu proje **Antigravity** ile yürütülür; komutlar Antigravity'ye hitap eder. Eski `CLAUDE.md` referansları varsa temizlenir. Tek agent, tek konvansiyon.

# EK C — CİHAZ STRATEJİSİ

- **Desktop (öncelikli):** 1920×1080, 2560×1440
- Tablet ve mobil: sonraki öncelik (Görsel Pass, Aşama 12)
- İlk sürüm masaüstü kullanıma optimize; responsive iyileştirme ertelenmiş.

# EK D — DOSYA ADI STANDARTLARI

- Rapor: `Folkart_Budget_Portfolio_Report_[reportPeriod]_[date].(pdf|xlsx)`
- Aşama dokümanları (bölünürse): `docs/asama-1.md` … `docs/asama-12.md`
- Fikir havuzu: `docs/fikir-havuzu.md`
- Eval seti: `docs/chat-eval.md`
- Checklist: `docs/deployment-checklist.md`, `docs/production-checklist.md`

# EK E — GENEL KISITLAR (her komuta örtük dahil)

- Mevcut dashboard tasarımını bozma (Görsel Pass hariç).
- Çalışan sayfaları/component'leri silme veya kırma.
- Büyük refactor yapma; küçük parça kuralına uy.
- Var olan dosyayı üzerine yazma (idempotency).
- Gerçek API/secret kurma (ilgili aşamaya kadar).
- Her aşama build hatasız bitecek.
- Her aşama sonunda onay bekle.

---

## SON SÖZ — Bu dokümanı nasıl kullanacaksın

1. Bu dosyayı Antigravity repo köküne `AGENTS.md` olarak koy.
2. Agent'a: "AGENTS.md'yi oku, DURUM tablosuna bak, sıradaki aşamayı uygula, loop kurallarına uy."
3. AŞAMA 1'den başla. Her TAMAM'dan sonra DURUM tablosu güncellenir, bir sonrakine geçilir.
4. Sıra: 1 → 2 → 3(a,b,c) → 4 → 5 → 6 → 7 → 8 → 9 → 10 → (isteğe bağlı 11, 12).
5. EAC metodu kararını ver; BÖLÜM 3'te kesinleştir.
