# Değişiklik Günlüğü

Bu dosya projedeki doğrulanmış değişiklikleri kaydeder. Biçim, Keep a Changelog yaklaşımından sadeleştirilmiştir.

## [Unreleased]

### Eklenenler
- Bütçe modülünde (WBS) aylık bütçe tablosuna **Planlanan** ve **Gerçekleşen** detay kırılımları (Miktar, Birim Fiyat, Tutar) eklendi.
- Tabloda en sağa **Kümülatif PV** ve **Kümülatif Gerçekleşen** sütunları eklendi.
- Aylık sütunları 3 kademeli (Ay -> Planlanan/Gerçekleşen -> Miktar/Birim Fiyat/Tutar) hiyerarşik tablo başlığına dönüştürüldü.
- Yaprak bütçe satırlarında `Tutar = Miktar x Birim Fiyat` ilişkisi kuruldu.
- Üst kategoriler (Ana/Alt PYP) için alt satırlardan otomatik bütçe/gerçekleşen maliyet toplama (roll-up) entegre edildi.
- Üst kategorilerde Miktar ve Birim Fiyat için `"—"` gösterimi sağlandı.
- **[Excel Akışı]** SheetJS (`xlsx`) kütüphanesi eklendi; tüm CSV üretimi kaldırıldı.
- **[Excel Akışı]** Veri Yükleme ekranında şablon sayısı 4'ten 3'e indirildi: `WBS_Maliyet.xlsx`, `Gerceklesen_Maliyet.xlsx`, `Hakedis.xlsx`.
- **[Excel Akışı]** "Şablon İndir" butonları artık gerçek `.xlsx` dosyası üretiyor (örnek satırlı).
- **[Excel Akışı]** Dosya yükleme artık SheetJS ile gerçekten parse ediliyor; zorunlu kolon kontrolü ve satır bazlı doğrulama uygulanıyor.
- **[Excel Akışı]** Doğrulama adımı gerçek hata/satır sayısını gösteriyor; hatalar liste olarak listeleniyor.
- **[Excel Akışı]** "Sisteme Aktar" artık parse edilen satırları WBS değer havuzuna yazıyor (yeni kaynak ekleyebilir veya mevcut BAC değerini güncelleyebilir).
- **[Excel Akışı]** `handleExportExcel` artık `.xlsx` üretiyor (Dashboard, S-Eğrisi, Nakit Akışı, Hakediş "Dışa Aktar" butonları).
- **[Excel Akışı]** Kaynak Listesi "Excel Şablonu İndir" gerçek `Kaynak_Listesi_Sablonu.xlsx` üretiyor; "Kaynak Listesi Yükle" dosyayı parse ediyor.
- **[Excel Akışı]** Dosya kabul listesinden `.csv` kaldırıldı; yalnızca `.xlsx / .xls` kabul ediliyor.

### Düzeltilenler — EVM Doğruluk Düzeltmesi (2026-07-02)
- **[EVM]** `src/app/evmEngine.ts`: `cpi`, `spi`, `eac`, `etc`, `vac`, `cvPct`, `svPct` alanlarında bölen sıfır/tanımsız olduğunda yanlışlıkla `0` dönme hatası düzeltildi; artık `null` (arayüzde "—") dönüyor. Bu, eksik veriyi "sıfır performans" gibi göstermeyi engeller (bkz. `PROJECT_ROADMAP.md` BÖLÜM 3.2 madde 7).
- **[EVM]** Eksik `TCPI = (BAC-EV)/(BAC-AC)` metriği `evmEngine.ts`'e eklendi.
- **[EVM]** `VarianceScreen` içinde roll-up sonrası oranları elle (`? : 0` deseniyle) yeniden hesaplayan tekrarlanan kod kaldırıldı; artık merkezi `calculateEVM` çağrılıyor (tek doğruluk kaynağı).
- **[EVM]** `fmtPct` artık `null`/`NaN` için "0.0%" yerine "—" döndürüyor; `cpiColor` `null` değeri nötr gri (#94a3b8) ile karşılıyor. Dashboard, Bütçe, EVM Analizi ve Sapma ekranlarındaki ilgili kartlar/tablo hücreleri bu null durumunu güvenli şekilde gösterecek biçimde güncellendi.
- Not: `S-Eğrisi` ve `Rapor` ekranlarında aynı "0 yerine N/A" desenine sahip, `calculateEVM`'den bağımsız iki adet manuel hesaplama (satır ~2389, ~2671) tespit edildi ancak bu turun kapsamı dışında bırakıldı; ayrı onayla ele alınmalı.

### Düzeltilenler — Kalan Bağımsız EVM Hesaplarının Merkezileştirilmesi (2026-07-02, devam)
- **[EVM]** `SCurveScreen`: yerel `cpi`/`spi` (`? : 0` deseni) ve statik `p.eac` kart değeri kaldırıldı; PV, `p.bac * (p.timePct/100)` ile türetilip `calculateEVM` çağrılıyor. CPI/SPI/EAC kartları null-safe.
- **[EVM]** `ReportScreen`: satır bazlı `const c = p.ac > 0 ? p.ev/p.ac : 0` ve toplam `totalEac = Σp.eac` kaldırıldı; hem satırlar hem portföy toplamı (`portfolioEvm`) merkezi `calculateEVM`'den geliyor. "Toplam EAC" kartındaki, işarete bakmaksızın hep "+" gösteren önceki hata da düzeltildi.
- **[EVM]** `PortfolioScreen`: `totalEac = Σp.eac` toplamı aynı şekilde merkezi motora yönlendirildi; aynı "hep +" işaret hatası düzeltildi. Ayrıca proje tablosunda hesaplanıp **hiçbir zaman render edilmeyen** ölü kod (`c`, `s`, `cv`) tespit edilip kaldırıldı (tablo başlığı 13 kolon tanımlıyor ama gövde 4 kolon render ediyor — bu eksik kolon durumu görünüm değişikliği gerektirdiği için bu turda dokunulmadı, sadece ölü hesaplama kodu temizlendi).
- **[EVM]** `VarianceScreen`: filtrelenmiş özet kartlarındaki (`totalCV/SV/CPI/SPI`) bağımsız hesaplama da merkezi `calculateEVM`'e yönlendirildi.
- Artık projede `calculateEVM`'den bağımsız, canlı olarak render edilen hiçbir CPI/SPI/EAC/CV/SV hesaplaması kalmadı (Glossary ekranındaki formül açıklamaları statik referans metnidir, hesaplama değildir).

### Test Sonucu
✓ PASS (npm run build başarılı — 2220 modül, chunk uyarısı normal)
✓ PASS — EVM düzeltmesi sonrası (2026-07-02, npm run build, 2220 modül, 8.62s, dist 1.167,64 kB)
✓ PASS — kalan bağımsız EVM hesaplarının merkezileştirilmesi sonrası (2026-07-02, npm run build, 2220 modül, 6.22s, dist 1.168,01 kB)
⚠ Lint: `package.json` içinde lint script'i tanımlı değil (N/A).
⚠ Otomatik test: proje genelinde npm test script'i yok; mevcut tek otomatik doğrulama `runWbsTests` (tarayıcı konsolu, bkz. `docs/development/TEST_REPORT.md`) bu oturumda headless çalıştırılamadı.

### Eklenenler — PortfolioScreen Eksik Kolonlar (2026-07-02, devam)
- **[Portföy]** `PortfolioScreen` proje tablosunda tanımlı ama render edilmeyen 9 kolon (Fiziki%, BAC, AC, EAC, CV, CPI, SPI, PM, Bitiş) eklendi. Mevcut yatay-scroll tablo deseni (Dashboard/Report ekranlarındaki ile aynı) korundu; yeni bir bileşen veya kolon seçici eklenmedi.
- **[Portföy]** Aynı tabloda kök-neden hatası düzeltildi: "Proje Adı" ve "Kategori" kolonları `Project` tipinde var olmayan `pr.name`/`pr.category` alanlarını okuyup boş render ediyordu; `pr.projectName`/`pr.projectType` ile değiştirildi.
- **[Portföy]** Yeni EAC/CV/CPI/SPI kolonu merkezi `evmEngine.ts` üzerinden, satır PV'si diğer ekranlarla aynı kuralla (`BAC x Plan%`) türetilerek hesaplanıyor.

### Test Sonucu
✓ PASS — PortfolioScreen kolonları sonrası (2026-07-02, npm run build, 2220 modül, 4.68s, dist 1.169,01 kB)

### Temizlenenler — Ölü Kod ve Araç Kısıtı (2026-07-02, devam)
- **[Temizlik]** Hiçbir yerde kullanılmayan `WBS_ROWS` placeholder mock dizisi kaldırıldı (kendi yorumu "bütçe ekranı yeniden yazılınca silinecek" diyordu; bütçe ekranı zaten yeniden yazıldı). Build çıktısı bit-bit aynı kaldı (aynı bundle hash'i).
- **[Araç kısıtı]** `typescript` paketini `devDependency` olarak ekleyip `tsc --noEmit` tabanlı bir typecheck script'i kurmayı denedim; `npm install` bu ortamda `SELF_SIGNED_CERT_IN_CHAIN` hatasıyla başarısız oluyor (kurumsal ağ/sertifika kısıtı — `npm config get cafile` boş, `strict-ssl=true`). Bunu aşmak için `strict-ssl` kapatmak bir güvenlik geriletmesi olacağından yapılmadı; bu bir repo/ortam politikası kararı olarak kullanıcıya raporlandı.

### Refactor — App.tsx Bölünmesi Faz 1 (2026-07-02, devam)
- **[Yapı]** `types.ts`, `mockData.ts`, `utils.ts` ayrıldı; ölü `versionAc`/`versionEv` yerelleri kaldırıldı; `MONTHS_LIST` reassign yerine mutate edilecek şekilde güncellendi (ES module kısıtı). Davranış değişmedi. `npm run build` PASS (2222 modül, aynı bundle boyutu).

### Temizlik + Refactor Faz 2 (2026-07-02, Fable 5 oturumu)
- **[Temizlik]** Depoya commit edilmiş ölü `src/app/App.tsx.bak` (1371 satır) git'ten kaldırıldı.
- **[Repo politikası]** `.gitignore` yalnızca `node_modules` (tekrarlı) içeriyordu; `dist/` eklendi ve önceden takip edilen `dist/` çıktısı takipten çıkarıldı (kullanıcı onayıyla).
- **[Yapı]** Ortak UI bileşenleri `components/shared.tsx`'e taşındı (Chip, Btn, ProgressBar, CpiGauge, MetricCard, Card, CardHeader, PageHeader, ChartTooltipContent).
- **[Yapı]** Kendi kendine yeten 4 ekran `screens/` altına taşındı: Glossary, Users, ResourceList, Hakedis. Davranış korundu.
- **[Düzeltme]** `ResourceListScreen` Excel şablonu indirme, var olmayan `ana.id/ana.ad/alt.id/alt.ad` alanlarını okuyup 4 sütunu boş üretiyordu; `anaPyp/anaPypAdi/altPyp/altPypAdi` ile düzeltildi (taşınan dosyada).
- **Sonuç:** `App.tsx` 3981 → **3367 satır**. `npm run build` PASS (2222 modül). Dev server ile önyükleme + tüm yeni modüllerin transpile'ı doğrulandı (`localhost:5173`, HTTP 200).

### Not — Araç Düzeltmesi
Önceki bir talimatta "Fable" adlı bir UI/UX tasarım aracının zorunlu kullanımı istenmişti; bu ortamda böyle bir araç bağlı değildir (yalnızca `claude-fable-5` adlı bir model vardır). `AGENTS.md`'deki ilgili bölüm bunu netleştirecek şekilde güncellendi; UI/UX işi mevcut tasarım diline sadık kalınarak doğrudan kodda yürütülüyor.

---

Version : v0.10.0

Eklenenler
------------
✓ Proje künyesi (Proje Kodu, Adı, Revizyon, Para Birimi)
✓ Master WBS bazlı dinamik Varyans tablosu (17 kolon)
✓ 3 kademeli drill-down interaktif Varyans grafiği (Ana PYP → Alt PYP → Kaynak)
✓ Proje, Ana PYP, Alt PYP, Kaynak, Durum, Tarih Aralığı filtreleri
✓ Filtre durumuna göre güncellenen dinamik metrik kartları
✓ Sürüm yedekleme sistemi (versions/ klasöründe kopyalar)

Düzeltilenler
--------------
✓ 13 ekranın tamamı açık temaya uyarlandı (LOOP-01 tamamlandı)
✓ Tablo ve toggle butonlardaki koyu renkler
✓ Chart kılavuz çizgileri ve metin renkleri

Silinenler
------------
✓ Demo WBS yapısı (Toprak İşleri → Kaplama İşleri) Varyans sayfasından kaldırıldı
✓ Eski VARIANCE_DATA mock verisi kaldırıldı

Test Sonucu
------------
✓ PASS (npm run build başarılı, visual smoke test onaylandı)

---

## [0.0.1] — 2026-06-28

### Eklendi

- Folkart Kurumsal İnşaat Proje Platformu ürün hedefi ve kapsamı belgelendi.
- Claude Code, Codex ve Antigravity için ortak çalışma kuralları `AGENTS.md` içine eklendi.
- Öncelikli iş kuyruğu ve tamamlanma ölçütleri `TASKS.md` içinde oluşturuldu.
- Oturumlar arası devralma özeti `.claude/MEMORY.md` içine eklendi.

### Analiz edildi

- React/Vite/Tailwind tabanlı mevcut prototip ve 13 ekranlık `App.tsx` yapısı.
- Mock veri kullanımı, hard-coded koyu tema, CONSTRUX marka kalıntıları ve monolitik dosya yapısı.
- Firebase yapılandırma dosyalarının henüz bulunmadığı, Vite'ın production için `dist/` çıktısı ürettiği.
- Ana JavaScript paketinin yaklaşık 673 kB olduğu ve build sırasında chunk-size uyarısı verdiği.

### Doğrulandı

- Kaynak kod değiştirilmeden `npm run build` başarıyla tamamlandı.
- Build sonucu: 2.218 modül dönüştürüldü; production çıktısı başarıyla üretildi.

### Değiştirilmedi

- Uygulama kaynak kodu, davranışları, mock veriler ve yapılandırma dosyaları.
- Risk, Monte Carlo ve Hedge kapsam dışında tutuldu.
