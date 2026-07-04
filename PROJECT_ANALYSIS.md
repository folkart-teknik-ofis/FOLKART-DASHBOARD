# PROJECT_ANALYSIS.md — Depo Analiz Geçmişi

> Bu dosya sadece **geçmiş analiz kayıtlarını** tutar. Kural/metodoloji için `AGENTS.md`,
> uzun vadeli vizyon/roadmap için `PROJECT_ROADMAP.md` kullanılır. Yeni analizler bu
> dosyanın SONUNA eklenir; önceki kayıtlar asla silinmez veya üzerine yazılmaz.

---

## Analiz — 2026-07-02

**Kapsam:** Tüm depo (yapı, git durumu, `package.json`, `src/`, `docs/`, EVM motoru,
import/export akışı, Firebase entegrasyonu, mimari tutarlılık, teknik borç, build durumu).
Kod değiştirilmedi, refactor yapılmadı; sadece inceleme.

### Yöntem / kontrol edilenler
- `AGENTS.md`, `PROJECT_ROADMAP.md`, `TASKS.md`, `CHANGELOG.md`, `README.md` okundu.
- `git status`, `git log -5`, `git diff --stat` ile mevcut çalışma kopyası durumu çıkarıldı.
- `src/` altındaki tüm `.ts/.tsx` dosyaları listelendi; `src/services/` arandı (yok).
- `firebase*` deseni ile tüm depoda Firebase yapılandırma dosyası arandı (yok).
- `docs/` altındaki tüm dosyalar listelendi.
- `App.tsx` içinde `calculateEVM`, `XLSX`, `firebase`, `auth`, `mock` geçişleri grep'lendi.
- `npm run build` çalıştırıldı.

### A) Depo yapısı
- `src/main.tsx` tek giriş noktası.
- `src/app/App.tsx` — **4.923 satır**, tek dosyada 13 ekran + ortak bileşenler + mock veri.
  Daha önceki kayıtlarda (AGENTS.md, TASKS.md, `.claude/MEMORY.md`) bu dosya "~2.100 satır"
  olarak belgelenmişti; gerçek boyut **2,3 kat büyümüş** ve hiçbir belgede güncellenmemiş.
- `src/app/evmEngine.ts` — yeni, 43 satır, saf hesaplama modülü (bkz. EVM bölümü).
- `src/app/components/ui/*` — geniş Radix/shadcn bileşen seti (çoğu ana uygulamada kullanılmıyor).
- **Yok:** `src/services/`, `src/types/`, `src/config/`, `src/lib/`,
  `src/components/upload/`, `src/components/reports/`. `PROJECT_ROADMAP.md` Aşama 3/4/6
  hedef klasör yapısının hiçbiri henüz kurulmadı; tek istisna `evmEngine.ts` (yanlış
  konumda: hedef `src/services/evmService.ts` idi, gerçekte `src/app/evmEngine.ts`).
- `docs/` sadece `docs/development/{CHANGELOG,DEVELOPMENT_LOG,KNOWN_ISSUES,TEST_REPORT}.md`
  içeriyor. Roadmap'in beklediği `docs/deployment-checklist.md`, `docs/production-checklist.md`,
  `docs/chat-eval.md`, `docs/fikir-havuzu.md` yok (beklenen — ilgili aşamalar başlamadı).
- Depoda `firebase.json`, `.firebaserc`, `firebase.ts` **yok**. Firebase/Auth entegrasyonu
  hiç mevcut değil; `App.tsx` içinde "firebase" veya "auth" geçen tek satır yok.

### B) Git durumu ve son değişiklikler
- Son gerçek commit: `4b36d7d0 Build_v0.10.0: Master WBS Variance screen conversion and full light theme`.
- Staged: `CHANGELOG.md` (M), `dist/` eski çıktıların silinmesi, `docs/development/*` (4 yeni dosya),
  `package-lock.json` (yeni), `package.json` (M), `src/app/App.tsx` (M, ~2.881 satır net değişim),
  `src/app/evmEngine.ts` (yeni).
- Unstaged: `src/app/App.tsx` üzerinde ek küçük değişiklik.
- Untracked: `PROJECT_ANALYSIS.md` (bu dosya), `PROJECT_ROADMAP.md`, `dist/`.
- Sonuç: yaklaşık **3.400+ satırlık** WBS/Excel/EVM çalışması hâlâ commit edilmemiş durumda;
  bu boyutta bir değişiklik grubu, önceki analizde de belirtildiği gibi, TASKS.md/CHANGELOG.md
  ile eş zamanlı güncellenmemiş.

### C) package.json
- Yeni bağımlılık: `xlsx: ^0.18.5` (SheetJS) — Excel import/export için.
- `firebase`, `jsPDF`/`pdfmake` gibi Roadmap Aşama 6/7/8 bağımlılıkları eklenmemiş (beklenen,
  ilgili aşamalar başlamadı).
- `@radix-ui/react-toggle` / `react-toggle-group` sıralaması değişmiş (kozmetik, davranış etkisi yok).

### D) EVM motoru (`src/app/evmEngine.ts`)
- `calculateEVM(bac, pv, ac, ev)` merkezi fonksiyonu `App.tsx` içinde **5 farklı noktada**
  çağrılıyor (portföy toplamı, proje toplamı, WBS satırı, kaynak seviyesi, Varyans ekranı).
  Bu, PROJECT_ROADMAP BÖLÜM 3.8 kuralına ("EVM hesabı component içinde yapılmaz, tek
  merkez") kısmen uyum sağlıyor — formüller tek yerde, ama servis katmanı yok, doğrudan
  component içinden çağrılıyor.
- Formüller Roadmap BÖLÜM 3.1/3.2 ile **örtüşüyor**: `CV=EV-AC`, `SV=EV-PV`, `CPI=EV/AC`,
  `SPI=EV/PV`, `EAC=BAC/CPI` (varsayılan, onay bekleyen yöntem), `ETC=EAC-AC`, `VAC=BAC-EAC`.
- **Tespit edilen sapmalar:**
  1. `TCPI` (`(BAC-EV)/(BAC-AC)`) Roadmap'te zorunlu türev metrik olarak listeleniyor ama
     `EVMResult` arayüzünde ve `calculateEVM` içinde **yok**.
  2. Bölme koruması `cpi = ac > 0 ? ev/ac : 0` şeklinde — sıfıra bölünürse sonuç **0**
     dönüyor. Roadmap BÖLÜM 3.2 madde 7 açıkça "fiziksel ilerleme yoksa EV/CPI/SPI = 'N/A'
     olmalı, hata verilmez ama sessizce 0 da gösterilmez" diyor. Şu anki davranış, veri
     eksikliğini gerçek bir "0 performans" gibi göstererek yanıltıcı olabilir — EVM
     doğruluğu açısından izlenmesi gereken bir risktir.
  3. AC'nin EV'den türetilmediği doğrulandı (fonksiyon imzasında ayrı parametre) — kritik
     kural burada ihlal edilmiyor.

### E) Import/export akışı
- Tamamen `App.tsx` içine gömülü, `xlsx` kütüphanesi doğrudan kullanılıyor (`XLSX.read`,
  `XLSX.utils.sheet_to_json`, `XLSX.utils.book_new/json_to_sheet`) — ayrı bir
  `excelImportService.ts` / `excelExportService.ts` yok.
- Şablon indirme (WBS_Maliyet, Gerceklesen_Maliyet, Hakedis, Kaynak_Listesi) ve yükleme
  akışı çalışıyor durumda (`docs/development/TEST_REPORT.md`: 18/18 test PASS).
- Roadmap Aşama 4/5'in öngördüğü "başlık adına göre kolon eşleme, SAP/LivaBudget format
  algılama, Türkçe karakter toleransı" **yok**; mevcut akış yalnızca uygulamanın kendi
  ürettiği sabit şablonlara bağlı. Bu, AGENTS.md kapsamı için yeterli ama Roadmap'in
  hedeflediği kurumsal import motorundan açık biçimde geridedir.
- Import batch kaydı, duplicate kontrolü, audit log — yok; veri yalnızca component state'inde.

### F) Firebase entegrasyonu
- Sıfır: yapılandırma dosyası, `firebase` paketi, auth/Firestore çağrısı yok. Roadmap
  Aşama 7–10 ile tutarlı biçimde henüz başlanmamış.

### G) Mimari tutarlılık
- İki paralel CHANGELOG hâlâ mevcut: kök `CHANGELOG.md` ve `docs/development/CHANGELOG.md`
  — aynı WBS çalışmasının örtüşen ama birebir aynı olmayan kayıtlarını tutuyor, birleştirilmemiş.
- `TASKS.md` son güncelleme tarihi hâlâ 2026-06-28; Excel/xlsx işi, `evmEngine.ts`, ve
  v0.10.0 Varyans ekranı commit'i TASKS.md'de hiç geçmiyor.
- `.claude/MEMORY.md` da aynı şekilde güncel değil.
- `AGENTS.md` "Bilinen teknik durum" bölümü hâlâ "~2.100 satır" ve "~673 kB" rakamlarını
  taşıyor — ikisi de artık yanlış (gerçek: 4.923 satır, 1.166,98 kB / gzip 342,72 kB).

### H) Teknik borç / bitmemiş işler
1. `App.tsx` 2.100 → 4.923 satıra çıkmış; TASKS.md P1'deki "ekranları ayrı dosyalara
   ayır" maddesi hâlâ işaretsiz ve dosya büyüdükçe bu iş maliyetlenmeye devam ediyor.
2. Ana JS paketi ~673 kB → **1.166,98 kB** (gzip 342,72 kB); Vite 500 kB uyarı eşiğinin
   belirgin biçimde üzerinde. Lazy loading / code-splitting hâlâ yapılmadı.
3. `evmEngine.ts`'de eksik `TCPI` ve "N/A yerine 0" sorunu (bkz. D.1–D.2).
4. Excel import genel amaçlı SAP/LivaBudget eşlemesi değil, yalnızca kendi şablonuna bağlı.
5. İki paralel CHANGELOG sistemi ve güncel olmayan TASKS.md/`.claude/MEMORY.md` — önceki
   analizde de tespit edilmişti, hâlâ çözülmedi.
6. ~3.400 satırlık uncommitted değişiklik grubu (App.tsx + evmEngine.ts + package.json)
   commit edilmeden bekliyor.

### I) Build durumu
**PASS.** `npm run build` → 2.220 modül dönüştürüldü, 6,85 sn.
- `dist/index.html`: 0,83 kB
- `dist/assets/index-*.css`: 106,95 kB (gzip 17,24 kB)
- `dist/assets/index-*.js`: **1.166,98 kB** (gzip 342,72 kB) — Vite chunk-size uyarısı var
  (hata değil, AGENTS.md'nin kabul ettiği türden bir uyarı, ancak boyut önceki kayda göre
  belirgin biçimde büyümüş).

---

### 1. Yönetici Özeti (Executive Summary)
Build temiz geçiyor ve WBS/Excel/EVM çalışması işlevsel olarak çalışıyor (18/18 test PASS,
formüller Roadmap ile uyumlu). Ancak bu iş büyük ölçüde belgelenmeden ve commit edilmeden
ilerlemiş: `App.tsx` sessizce 2,3 kata büyümüş, ana bundle neredeyse ikiye katlanmış,
TASKS.md/CHANGELOG.md/`.claude/MEMORY.md` günler öncesinde donmuş durumda. Roadmap'in
öngördüğü servis katmanı (`services/`, `types/`, `lib/`) hâlâ yok; tek somut adım
`evmEngine.ts` ve o da hedeflenen konumda değil. Firebase/Auth'a hiç dokunulmamış —
bu, AGENTS.md'nin "gerçek backend varmış gibi varsayım yapma" kuralıyla tutarlı.

### 2. Kritik Sorunlar (Critical Issues)
- **EVM doğruluk riski:** `cpi`/`spi` sıfıra bölünürken "N/A" yerine `0` dönüyor — Roadmap'in
  kendi "ihlal edilemez kural"ı ile çelişiyor, yönetim raporlarında yanlış "sıfır performans"
  görüntüsü verebilir.
- **Eksik metrik:** `TCPI` motorda yok, ama Roadmap ve muhtemel raporlama ihtiyaçları bunu
  zorunlu tutuyor.
- **Belge/kod senkron kaybı:** AGENTS.md'nin "bilinen teknik durum" rakamları (satır sayısı,
  bundle boyutu) yanlış; TASKS.md ve `.claude/MEMORY.md` en az 4 gündür güncellenmemiş; iki
  paralel CHANGELOG hâlâ birleştirilmemiş.
- **Commit edilmemiş büyük değişiklik:** ~3.400 satırlık iş, doğrulanmış görünmesine rağmen
  henüz bir commit altında kayıt altına alınmamış.

### 3. Uygulama Önceliği (Implementation Priority)
1. `evmEngine.ts` içindeki "N/A" davranışını ve eksik `TCPI`'yi düzelt (EVM doğruluğu, Roadmap
   BÖLÜM 3 açık kural).
2. Mevcut doğrulanmış WBS/Excel/EVM işini commit et; CHANGELOG.md (kök) ve TASKS.md'yi bu işi
   yansıtacak şekilde güncelle.
3. TASKS.md, `.claude/MEMORY.md` ve AGENTS.md'nin "bilinen teknik durum" rakamlarını gerçek
   duruma (4.923 satır, 1.166,98 kB) güncelle.
4. İki CHANGELOG sistemini birleştir veya `docs/development/*`'i AGENTS.md'nin belge
   sorumlulukları bölümünde resmen tanımla.
5. Ardından, kullanıcı onayına bağlı olarak: `App.tsx` bölme planı (P1) ve bundle
   küçültme/lazy-loading çalışmasına geç.

### 4. Önerilen Sıradaki Adım (Recommended Next Step)
Kod değişikliğine geçmeden önce: (a) mevcut uncommitted değişiklik grubunu gözden geçirip
commit etme kararını onaylat, (b) `evmEngine.ts`'deki N/A/TCPI düzeltmesinin kapsamını
onaylat — bu ikisi doğrulanmadan TASKS.md/CHANGELOG.md güncellemesi veya başka bir kod
değişikliği başlatılmamalı.

**Onay bekleniyor.**

---

## Analiz — 2026-07-02 (Devam — EVM Doğruluk Düzeltmesi)

**Kapsam:** Kullanıcı onayıyla (adım adım, "onay bekleme" talimatıyla) sadece `evmEngine.ts`
ve doğrudan tüketicileri kapsayan dar bir düzeltme uygulandı. AGENTS.md döngüsüne göre:
Analiz → Doğrula → Planla → Uygula → Test Et → Dokümantasyonu Güncelle → Tekrar Doğrula → Commit Öner.

### Uygulanan değişiklikler
1. `src/app/evmEngine.ts`: `EVMValue = number | null` tipi eklendi; `cpi`, `spi`, `eac`,
   `etc`, `vac`, `cvPct`, `svPct` artık bölen 0/tanımsızken `0` değil `null` dönüyor.
   Eksik `TCPI = (BAC-EV)/(BAC-AC)` metriği eklendi.
2. `App.tsx`: `fmtPct` null için "0.0%" yerine "—" döndürecek, `cpiColor` null için nötr
   gri (#94a3b8) dönecek şekilde güncellendi.
3. `WBSFinanceItem` arayüzü (iki kopyası da) `cvPct/svPct/cpi/spi` alanlarını
   `number | null` olarak güncellendi.
4. `getProjectWBSFinance` içinde `cvPct`/`svPct` null-safe hesaplanıyor; sınıflandırma
   (`worstPct`) için eksik veri yalnızca dahili eşikleme amacıyla nötr (0) kabul ediliyor,
   gösterimde gerçek değer (null → "—") korunuyor.
5. `VarianceScreen` içindeki tekrarlanan manuel `? : 0` hesaplaması kaldırıldı; roll-up
   sonrası oranlar merkezi `calculateEVM` ile yeniden hesaplanıyor (mimari iyileştirme —
   "tek merkez" kuralına bir adım daha yaklaşıldı).
6. Dashboard, Bütçe (`evmTotals`), EVM Analizi (`eacMethods`/`dev`) ve Sapma ekranlarındaki
   ilgili kart/tablo hücreleri null-safe render edecek şekilde güncellendi (`.toFixed()`
   çökme riski olan noktalar dahil, örn. `evmTotals.eac.toFixed(3)`).

### Bilinçli olarak KAPSAM DIŞI bırakılanlar (raporlanıyor, dokunulmadı)
- `S-Eğrisi` ekranı (~satır 2389-2390) ve `Rapor` ekranı (~satır 2671): `calculateEVM`'den
  bağımsız, aynı "0 yerine N/A" anti-desenine sahip iki ayrı manuel CPI/SPI hesaplaması.
  Bunlar `Project` mock tipinin düz `number` alanlarını kullandığı için bu turun tip
  değişikliğinden etkilenmedi (çökme riski yok) ama aynı doğruluk sorununu taşıyorlar.
  Düzeltme, mevcut dar kapsamı aşıp büyük refactor sınırına girmemek için bilerek
  bırakıldı — ayrı onayla ele alınmalı.
- İki adet birebir aynı `interface WBSFinanceItem` tanımı (satır ~495 ve ~515) — zararsız
  ama gereksiz kod tekrarı; dokunulmadı.
- `App.tsx`'in bölünmesi, bundle küçültme, iki paralel CHANGELOG sistemi, AGENTS.md'deki
  güncel olmayan satır/boyut rakamları — önceki analiz kaydında tespit edildi, bu turda
  kapsam dışı bırakıldı.

### Doğrulama
- `npm run build`: **PASS** — 2.220 modül, 8,62 sn, `dist/assets/index-*.js` 1.167,64 kB
  (gzip 342,93 kB). Önceki build ile pratik olarak aynı boyut (+0,66 kB).
- Lint script'i projede tanımlı değil → N/A.
- Otomatik test script'i (`npm test`) projede tanımlı değil → N/A. Mevcut tek otomatik
  doğrulama paketi (`runWbsTests`) tarayıcı konsolu bazlı; bu oturumda headless
  çalıştırılamadı, bu nedenle "test edildi" diye rapor edilmiyor.
- Formüller BÖLÜM 3.1/3.2 ile tekrar karşılaştırıldı: CV, SV, CPI, SPI, EAC(varsayılan
  BAC/CPI), ETC, VAC, TCPI hepsi roadmap ile uyumlu; AC hâlâ EV'den türetilmiyor.

### 1. Yönetici Özeti
`evmEngine.ts`'deki "eksik veriyi 0 gösterme" hatası ve eksik TCPI metriği düzeltildi;
değişiklik yalnızca gerçekten etkilenen tüketim noktalarına (Dashboard, Bütçe, EVM
Analizi, Sapma ekranları) yayıldı, build temiz geçti. Aynı anti-desenin S-Eğrisi ve Rapor
ekranlarında bağımsız kopyaları olduğu tespit edildi ama bilinçli olarak bu turun dışında
bırakıldı.

### 2. Kritik Sorunlar
- S-Eğrisi ve Rapor ekranlarındaki bağımsız CPI/SPI hesaplamaları hâlâ "0 yerine N/A"
  hatasını taşıyor (bu turda dokunulmadı).
- Otomatik test/lint script'i olmadığı için doğrulama tamamen build + manuel kod
  incelemesine dayanıyor; kurumsal kalite hedefi için bu bir açık.

### 3. Uygulama Önceliği
1. Onay alınırsa: S-Eğrisi ve Rapor ekranlarındaki bağımsız CPI/SPI hesaplamalarını da
   merkezi `evmEngine.ts`'e yönlendir.
2. `npm test`/lint script'i ekleme kararını değerlendir.
3. Mevcut ~3.400 satırlık commit edilmemiş çalışma + bugünkü EVM düzeltmesi için commit.

### 4. Önerilen Sıradaki Adım
Bu değişiklik grubunu (WBS/Excel/EVM işi + bugünkü N/A/TCPI düzeltmesi) tek, anlamlı bir
commit ile kayıt altına al; ardından S-Eğrisi/Rapor ekranlarındaki bağımsız hesaplamaların
düzeltilip düzeltilmeyeceğine karar ver.

---

## Analiz — 2026-07-02 (Devam — Kalan Bağımsız EVM Hesaplarının Merkezileştirilmesi)

**Kapsam:** Kullanıcının verdiği genel yetkiyle (küçük teknik kararlarda tekrar onay
istenmeden), önceki analizde "kapsam dışı" olarak işaretlenen S-Eğrisi/Rapor ekranları
dahil, projedeki TÜM bağımsız CPI/SPI/EAC/CV hesaplamaları tarandı ve merkezi
`evmEngine.ts`'e yönlendirildi.

### Bulunan ve düzeltilen bağımsız hesaplamalar
1. **SCurveScreen** (~satır 2391-2392, 2422): yerel `cpi`/`spi` (`? : 0` deseni) ve statik
   `p.eac` kart değeri. PV, `Project` tipinde ayrı bir alan olmadığı için
   `p.bac * (p.timePct/100)` ile türetilip `calculateEVM(bac, pv, ac, ev)` çağrıldı.
2. **ReportScreen** (~satır 2629-2632, 2675, 2701-2704): satır bazlı `const c = p.ac>0 ?
   p.ev/p.ac : 0` ve portföy toplamı `totalEac = Σp.eac`. İkisi de merkezi `calculateEVM`'e
   (satır + portföy toplamı için ayrı ayrı) yönlendirildi. Ayrıca önceden işarete
   bakmaksızın hep `"+"` gösteren bir görüntüleme hatası (kök neden: string literal'e
   sabit `+` eklenmesi) düzeltildi.
3. **PortfolioScreen** (~satır 881-883, 894, 920-923): `totalEac = Σp.eac` merkezi motora
   yönlendirildi (aynı "hep +" hatası burada da vardı, düzeltildi). Ayrıca tabloda
   hesaplanıp **hiç render edilmeyen** ölü kod (`c`, `s`, `cv`) bulundu — tablo başlığı 13
   kolon tanımlıyor, gövde yalnızca 4 kolon render ediyor. Bu, önceden var olan eksik bir
   tablo/kolon durumu; tamamlamak görünür bir UI değişikliği olacağından (onay kategorisi
   #2) bu turda dokunulmadı, yalnızca artık hiçbir yerde kullanılmayan ölü hesaplama
   silindi.
4. **VarianceScreen** (~satır 1868-1873): filtrelenmiş özet kartlarındaki
   `totalCV/SV/CPI/SPI` hesaplaması da merkezi `calculateEVM`'e yönlendirildi (bu ekranın
   satır bazlı `allFinData` hesaplaması zaten önceki oturumda düzeltilmişti).

### Doğrulama
- `npm run build`: **PASS** — 2.220 modül, 6,22 sn, `dist/assets/index-*.js` 1.168,01 kB
  (gzip 343,06 kB). Önceki ile pratik olarak aynı boyut.
- Dosya genelinde `cpi|spi|eac|cvPct|svPct|tcpi|etc|vac` için tam metin taraması yapıldı;
  geriye `calculateEVM`'den bağımsız, canlı render edilen hiçbir hesaplama kalmadığı
  doğrulandı. Glossary ekranındaki formül tanımları ve EVM_TREND grafik mock verisi
  statik referans/demo içeriktir, hesaplama değildir — dokunulmadı.
- Lint ve otomatik test script'i hâlâ tanımlı değil (bir önceki kayıtla aynı durum).

### 1. Yönetici Özeti
Projedeki EVM ile ilgili tüm ekranlar artık tek bir doğruluk kaynağından (`evmEngine.ts`)
besleniyor. Üç ekranda (S-Eğrisi, Rapor, Portföy) hem "0 yerine N/A" hem de görüntüleme
düzeyinde bir işaret hatası ("hep +") aynı anda düzeltildi. Bir ekranda (Portföy tablosu)
tamamen ölü/render edilmeyen hesaplama kodu bulunup temizlendi; bu, kodun daha önce
düşünüldüğünden daha "bitmemiş" olduğunu gösteriyor (13 kolonluk başlık, 4 kolonluk gövde).

### 2. Kritik Sorunlar
- PortfolioScreen tablosunun eksik 9 kolonu (Fiziki%, BAC, AC, EAC, CV, CPI, SPI, PM,
  Bitiş) hâlâ render edilmiyor — bu bir veri doğruluğu sorunu değil ama kullanıcıya
  yarım bırakılmış bir ekran gösteriyor olabilir; tamamlanması görünür bir UI kararı
  gerektirir.
- Otomatik test/lint script'i hâlâ yok.

### 3. Uygulama Önceliği
1. Onay verilirse: PortfolioScreen tablosunun eksik kolonlarını tamamla (görünür UX
   kararı — kullanıcı onayı gerekiyor).
2. `npm test`/lint script'i ekleme kararı.
3. `dist/` klasörünün commit stratejisi (bir önceki kayıttan beri açık).

### 4. Önerilen Sıradaki Adım
Bu turun değişikliklerini (evmEngine merkezileştirmesi + ölü kod temizliği) TASKS.md ve
CHANGELOG.md ile birlikte tek bir commit'te kayıt altına al; PortfolioScreen'in eksik
kolonları ve `dist/` stratejisi için kullanıcıdan görünür karar/politika onayı bekle.

---

## Analiz — 2026-07-02 (Devam — PortfolioScreen Eksik Kolonlar + Araç Düzeltmesi)

**Kapsam:** Kullanıcı, PortfolioScreen tablosundaki eksik 9 kolonu (Fiziki%, BAC, AC, EAC,
CV, CPI, SPI, PM, Bitiş) tamamlama onayını verdi ve "Fable" adlı bir UI/UX aracının
zorunlu kullanılmasını istedi.

### Araç doğrulaması (önemli bulgu)
`ToolSearch` ile iki kez arandı: bu ortamda "Fable" adında ayrı bir UI/UX tasarım aracı
**bağlı değil**. `claude-fable-5` yalnızca bir model adıdır (Sonnet/Opus/Haiku ile aynı
kategoride), Figma/Canva gibi bir tasarım uygulaması değildir. Bu, kullanıcıya doğrudan
raporlandı (uydurma/taklit araç kullanımı yapılmadı) ve `AGENTS.md`'deki "Fable
Zorunluluğu" bölümü, gerçek durumu yansıtacak şekilde "UI/UX Geliştirme Kuralı" olarak
güncellendi: tasarım artık doğrudan kodda, mevcut tasarım diline sadık kalınarak
yapılıyor; büyük/görünür kararlar yine kullanıcıya soruluyor.

### PortfolioScreen düzeltmesi
- Analiz sırasında tabloyu okurken ek bir kök-neden hata bulundu: "Proje Adı" ve
  "Kategori" kolonları `pr.name` / `pr.category` okuyordu, ama `Project` arayüzünde bu
  alanlar yok (gerçek alanlar `projectName` / `projectType`). Tip kontrolü olmadığı için
  (`tsconfig.json` yok, Vite/esbuild tipleri hiç denetlemiyor) bu hata build'i kırmadan
  sessizce boş hücre üretiyordu — muhtemelen uzun süredir fark edilmemiş bir görüntüleme
  hatası. Düzeltildi.
- Eksik 9 kolon eklendi; tasarım kararı olarak **yatay kaydırmalı tablo** (mevcut
  Dashboard/Report ekranlarıyla birebir aynı desen: `overflow-x-auto`, `text-xs`,
  `font-mono` sayısal hücreler, CV/CPI/SPI için yeşil/kırmızı renk kodlaması) seçildi;
  kolon seçici veya yeni bir bileşen türü eklenmedi (mevcut tasarım dilinde emsali yok,
  gereksiz karmaşıklık olurdu).
- Yeni EAC/CV/CPI/SPI hücreleri merkezi `calculateEVM`'den besleniyor; satır PV'si diğer
  ekranlarla aynı kuralla (`BAC x Plan%`) türetiliyor.

### Doğrulama
- `npm run build`: **PASS** — 2.220 modül, 4,68 sn, dist 1.169,01 kB (gzip 343,16 kB).
- Lint/test script'i hâlâ tanımlı değil (değişmedi).
- `AGENTS.md`'nin "Bilinen teknik durum" bölümündeki eski rakamlar (2.100 satır / 673 kB)
  gerçek değerlerle (~4.930 satır / ~1.169 kB) güncellendi — bu, önceki analizde tespit
  edilmiş ama daha önce uygulanmamış bir düzeltmeydi.

### 1. Yönetici Özeti
PortfolioScreen artık tanımlı tüm EVM kolonlarını gösteriyor ve bu sırada bulunan bağımsız
bir görüntüleme hatası (boş Proje Adı/Kategori) düzeltildi. Kullanıcının istediği "Fable"
aracı bu ortamda mevcut değil; bu şeffaf biçimde raporlandı ve AGENTS.md gerçek duruma göre
düzeltildi — geliştirme süreci bloke edilmedi, mevcut tasarım diliyle devam edildi.

### 2. Kritik Sorunlar
- Yok (bu turda yeni bir kritik sorun tespit edilmedi).

### 3. Uygulama Önceliği
1. `dist/` klasörünün commit stratejisi hâlâ açık (kullanıcı kararı bekliyor).
2. Projede tip denetimi (tsconfig + `tsc --noEmit` veya benzeri) olmaması, `pr.name`
   türünden hataların build'i kırmadan sessizce geçmesine izin veriyor — ileride bir
   `npm run typecheck` script'i eklenmesi değerlendirilebilir (kullanıcı onayı gerekir,
   çünkü yeni script/araç eklemek küçük bir repo politikası kararıdır).
3. `App.tsx` bölme planı (P1) ve bundle küçültme hâlâ bekliyor.

### 4. Önerilen Sıradaki Adım
Bu turun değişikliklerini (PortfolioScreen kolonları + kök-neden hata düzeltmesi +
AGENTS.md/Fable düzeltmesi) TASKS.md/CHANGELOG.md ile birlikte tek commit'te kayıt altına
al; `dist/` stratejisi ve olası bir typecheck script'i için kullanıcı kararını bekle.

---

## Analiz — 2026-07-02 (Fable 5 oturumu — Tam Depo Analizi + Refactor Faz 2)

**Model:** Fable 5. **Kapsam:** Kullanıcının 10 başlıklı tam analiz talebi + onaylanan 🔥
temizlik/refactor işleri. Build ve dev server GERÇEKTEN çalıştırılarak doğrulandı.

### A) Doğrulanan çalışan durum
- `npm run build`: **PASS** (2222 modül, ~6 sn, dist JS ~1.169 kB / gzip ~343 kB).
- `npm run dev`: **`http://localhost:5173/`**, `GET /` = HTTP 200, başlık "Kurumsal İnşaat
  Proje Platformu". `App.tsx`, `utils.ts`, `components/shared.tsx` ve taşınan ekran
  modülleri 200 (transpile + import çözümü başarılı). Server temiz kapatıldı.
- Lint/test script'i yok (N/A). Tarayıcıda 13 ekranın görsel/etkileşim doğrulaması
  yapılMAdı (bu ortamda tarayıcı otomasyonu yok) — yalnızca önyükleme + modül transpile
  doğrulandı, bu şeffaflıkla belirtildi.

### B) Bu oturumda tespit edilen ve düzeltilen sorunlar
1. **`src/app/App.tsx.bak` (1371 satır) git'e commit edilmişti** — ölü yedek, kaldırıldı.
2. **`.gitignore` `dist/`'i kapsamıyordu** (yalnızca tekrarlı `node_modules`); düzeltildi,
   `dist/` takipten çıkarıldı (repo politikası — kullanıcı onayı alındı).
3. **`ResourceListScreen` Excel şablonu** var olmayan `ana.id/ana.ad/alt.id/alt.ad`
   alanlarını okuyup 4 sütunu boş üretiyordu → `anaPyp/anaPypAdi/altPyp/altPypAdi`.
4. **TASKS.md P1 çelişkisi** (yapılmış "tipleri/mock/utils taşı" maddesi hâlâ `[ ]`) düzeltildi.

### C) Refactor Faz 2 (bu oturum)
- `components/shared.tsx` oluşturuldu (9 ortak bileşen).
- `screens/` altına 4 kendi kendine yeten ekran taşındı (Glossary, Users, ResourceList,
  Hakedis). Her taşımadan sonra build doğrulandı, davranış korundu.
- **`App.tsx` 3981 → 3367 satır** (bu oturum); oturum başındaki 4930'dan toplam düşüş belirgin.

### D) Kalan teknik borç (öncelik sırasıyla)
1. **9 ekran hâlâ App.tsx içinde** (Dashboard, SCurve, Report, CashFlow, EVM, Portfolio,
   Variance, Budget, DataUpload) — props/App-state bağımlı; en büyükleri Budget (~665) ve
   DataUpload (~594) ve App() kabuğu (~730). Faz 2 devam etmeli.
2. **Tip denetimi yok** (tsconfig yok) — bu oturumda 2 sessiz alan hatası bu yüzden
   yakalanamadan üretime girmişti (`pr.name`, `ana.id`). `npm install` `SELF_SIGNED_CERT_IN_CHAIN`
   ile bloke; `NODE_EXTRA_CA_CERTS`/kurumsal kök sertifika gerekiyor (ortam kararı).
3. **Ekran bölme bitince lazy-loading** ile 1.17 MB bundle bölünebilir (bağımlı iş).
4. İki paralel CHANGELOG; `docs/PROJECT_ANALYSIS.md` yok (analiz kökte) — belge tutarlılığı.

### E) Mimari hazırlık (roadmap) — özet
Firebase/Auth/rol/SAP/API/AI Chat için **henüz hazır değil** (servis katmanı, veri
soyutlaması, config/.env iskeleti yok). Mevcut aşama için normal. Önce servis katmanı
soyutlaması (mock arkasına `*Service` arayüzleri) gelmeli; aksi halde gerçek backend
entegrasyonu büyük ve riskli olur.

### Seviye ve tamamlanma (tahmin, kesin değil)
- Seviye: **gelişmiş prototip → erken MVP eşiği**. Beta/canlıya-yakın değil.
- Dar prototip hedefine göre ~%70-80; roadmap tam vizyonuna göre ~%15-20.
- En büyük darboğazlar: monolitik App.tsx (azalıyor), tek büyük bundle, `npm install` kilidi.
- En kritik sıradaki adım: Faz 2 ekran taşımayı sürdür (Dashboard'dan başla), sonra servis
  katmanı soyutlamasına geç.
