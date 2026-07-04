# Folkart Kurumsal İnşaat Proje Platformu — Görevler

Son güncelleme: 2026-07-02 (Claude Code — Excel akışı belgelendi, EVM N/A/TCPI düzeltmesi tamamlandı)

## Aktif durum

Proje çalışan bir Vite/React prototipidir. İlk UI LOOP tamamlandı: Dashboard ile ortak sol menü/üst bar açık Folkart görünümüne geçirildi; diğer 12 ekranın mevcut içerik yapısı korunarak menü erişimi doğrulandı. Ayrıca (daha önce belgelenmemiş) bir çalışma grubunda WBS bütçe tablosu Excel içe/dışa aktarım akışına ve merkezi bir EVM motoruna (`src/app/evmEngine.ts`) kavuştu; bugün bu motordaki bir doğruluk hatası (bkz. P0 altı) düzeltildi.

## Tamamlanan işler

- [x] `AGENTS.md`, `CLAUDE.md`, `CHANGELOG.md`, `.claude/MEMORY.md`, `package.json` ve `src/app/App.tsx` incelendi.
- [x] Mevcut teknoloji stack'i, ekran envanteri ve dosya yapısı çıkarıldı.
- [x] Ürün hedefi “Folkart Kurumsal İnşaat Proje Platformu” olarak belgelendi.
- [x] Açık tema, mevcut yapıyı koruma, Firebase deploy uyumu ve kapsam dışı konular ortak kurallara işlendi.
- [x] Claude Code, Codex ve Antigravity için ortak çalışma/devralma akışı tanımlandı.
- [x] Başlangıç production build'i doğrulandı (`npm run build`, 2026-06-28).
- [x] Dashboard açık gri zemin, beyaz kartlar, açık sınırlar ve kurumsal lacivert vurgu ile yenilendi.
- [x] Sol menü, üst bar, arama alanı ve bildirim açılır paneli açık kurumsal kabuğa geçirildi.
- [x] Görünür CONSTRUX metinleri kaldırıldı; platform adı “Kurumsal İnşaat Proje Platformu” olarak uygulandı.
- [x] Portföy mock verileri 3022 Nova, 3025 Orion, 3028 Mona, 3036 Çandarlı ve 3013 Terra ile güncellendi.
- [x] Dashboard performans tablosu 1280 px masaüstünde yatay scroll üretmeyecek şekilde sıkılaştırıldı.
- [x] 13 ekranın tamamı menüden açılarak beklenen sayfa başlığıyla render olduğu doğrulandı.
- [x] İlk UI LOOP production build'i doğrulandı (`npm run build`, 2026-06-28).
- [x] WBS bütçe tablosuna Excel içe/dışa aktarım (SheetJS/`xlsx`) entegre edildi: şablon indirme, kolon/satır doğrulama, "Sisteme Aktar" ve `.xlsx` dışa aktarım akışları (bkz. `CHANGELOG.md` [Unreleased]).
- [x] EVM hesapları `src/app/evmEngine.ts` içinde merkezi hale getirildi ve `App.tsx` içindeki birden fazla noktadan çağrılıyor.
- [x] `evmEngine.ts`'de bölüm-sıfır durumunda yanlış "0" dönme hatası düzeltildi: `cpi`, `spi`, `eac`, `etc`, `vac`, `cvPct`, `svPct` artık veri yokken `null` döner, arayüzde "—" gösterilir. Eksik `TCPI` metriği PMBOK/`PROJECT_ROADMAP.md` BÖLÜM 3 ile uyumlu şekilde eklendi. `VarianceScreen` içindeki tekrarlanan manuel oran hesaplaması kaldırılıp merkezi motora yönlendirildi (2026-07-02, `npm run build` PASS).
- [x] `SCurveScreen`, `ReportScreen`, `PortfolioScreen` ve `VarianceScreen`'in filtrelenmiş özet kartlarındaki bağımsız CPI/SPI/EAC/CV hesaplamaları merkezi `evmEngine.ts`'e yönlendirildi; `PortfolioScreen` tablosunda hesaplanıp hiç render edilmeyen ölü kod (`c`, `s`, `cv`) temizlendi (2026-07-02, `npm run build` PASS).
- [x] `PortfolioScreen` proje tablosundaki eksik 9 kolon (Fiziki%, BAC, AC, EAC, CV, CPI, SPI, PM, Bitiş) görünür hale getirildi; mevcut yatay-scroll tablo tasarım dili korundu, yeni bileşen/etkileşim eklenmedi. Aynı satırda "Proje Adı"/"Kategori" kolonlarının var olmayan `pr.name`/`pr.category` alanlarını okuyup boş render ettiği kök-neden hatası da düzeltildi (`pr.projectName`/`pr.projectType`) (2026-07-02, `npm run build` PASS).

## P0 — İlk güvenli geliştirme adımı

- [x] Mevcut ekran ve etkileşimlerin kısa kabul kriterlerini çıkar; dönüşüm öncesi görsel referans al.
- [x] `src/styles/theme.css` içinde Folkart'a uygun semantik açık tema tokenlarını tanımla.
- [x] Uygulama kabuğunu ve Dashboard bileşenlerini açık tema varyantlarına geçir.
- [x] İlk adımda mock veri şekillerini, hesaplamaları, ekran listesini ve navigasyon davranışını değiştirme.
- [x] CONSTRUX marka metinlerini kontrollü biçimde Folkart ürün kimliğiyle değiştir.
- [x] Masaüstü görünümde temel görsel smoke test yap.
- [x] `npm run build` başarılı olmadan bu maddeyi tamamlandı sayma.
- [x] Kalan 12 içerik ekranını ortak açık tema sistemine geçir. (LOOP-01 tamamlandı, 2026-06-28)
- [x] Ortak renkleri semantik tokenlarda merkezileştirip geçici hard-coded açık renkleri azalt.

## P1 — Yapıyı güvenli biçimde sadeleştirme

- [~] `App.tsx` bölünmesi devam ediyor. Faz 1: `types.ts`/`mockData.ts`/`utils.ts` ayrıldı. Faz 2: `components/shared.tsx` + 4 kendi kendine yeten ekran (`Glossary`, `Users`, `ResourceList`, `Hakedis`) `screens/`'e taşındı. App.tsx 4930→**3367 satır**. **Kalan:** 9 ekran (Dashboard, SCurve, Report, CashFlow, EVM, Portfolio, Variance, Budget, DataUpload) — props/state bağımlı, en son Budget/DataUpload.
- [x] Ortak tipleri, mock verileri ve format yardımcılarını ayrı modüllere taşı (`types.ts`/`mockData.ts`/`utils.ts`, 2026-07-02).
- [x] Depoya sızmış ölü `src/app/App.tsx.bak` yedeği git'ten kaldırıldı; `.gitignore` `dist/`'i kapsayacak şekilde düzeltildi ve `dist/` takipten çıkarıldı (2026-07-02).
- [ ] Kullanılmayan bağımlılıkları ancak gerçek kullanım analizi ve başarılı build sonrasında değerlendir.
- [ ] URL/route ihtiyacını belirle; Firebase SPA rewrite gereksinimini netleştirmeden router geçişi yapma.
- [ ] Büyük ana bundle için ölçüm yap ve gerekirse ekran bazlı lazy loading planla.

## P2 — Ürünleştirme

- [ ] Folkart proje/portföy terminolojisini ve yetki rollerini ürün sahibiyle doğrula.
- [ ] Mock verilerden gerçek veri katmanına geçiş için servis arayüzü tasarla.
- [ ] Firebase Hosting hedefini, proje kimliğini ve SPA rewrite ayarını doğrula; mevcut deploy akışını bozmadan yapılandır.
- [ ] Kimlik doğrulama ve rol bazlı erişim gereksinimlerini netleştir.
- [ ] Excel/veri yükleme ekranı için gerçek doğrulama kurallarını tanımla.
- [ ] Erişilebilirlik, responsive görünüm ve tarayıcı testlerini ekle.

## Kapsam dışı

- Risk modülü
- Monte Carlo simülasyonu
- Hedge işlemleri veya modelleri

Bu başlıklar ayrıca onay verilene kadar görevlere veya uygulama mimarisine eklenmeyecektir.

## Tamamlanma ölçütü

Bir görev ancak kapsamı karşılandığında, ilgili belgeler güncellendiğinde ve son `npm run build` başarılı olduğunda tamamlanmış kabul edilir.

---

## Antigravity Loop Planı

Son güncelleme: 2026-06-28 — Antigravity (devralma oturumu)

### Teşhis

Codex yalnızca uygulama kabuğunu (sidebar + header) açık temaya çekti. İçerik ekranları koyu kaldı. Bunun kökeni `App.tsx` satır 2137:

```tsx
<main className={`... ${screen === "dashboard" ? "bg-[#f4f7fb]" : "bg-[#080c14]"}`}>
```

`theme.css` tokenları hiç değiştirilmedi; hâlâ tam koyu tema değerleri (`--background: #080c14` vb.). Ortak bileşenler (`Card`, `MetricCard`, `Btn`) geçici `light` prop ile iki varyant taşıyor; gerçek token sistemi yok.

### Loop yapısı

Her loop şu adımları izler:

1. **Keşfet** — Etkilenecek dosyaları ve satırları tespit et
2. **Planla** — Değişikliği kullanıcıya sun, onay bekle
3. **Uygula** — Sadece onaylanan işi yap, kapsamı aşma
4. **Doğrula** — `npm run build` + 13 ekran smoke testi
5. **Yinele** — Kalan eksikleri raporla, sıradaki loop için karar iste

### LOOP-01 — Tema Token Merkezi + Tüm Ekranlar ✅ TAMAMLANDI (2026-06-28)

**Neler yapıldı:**
- `src/styles/theme.css` — Folkart açık tema tokenları uygulandı (`#f4f7fb`, `#123b6d`, `#ffffff` vb.)
- `src/app/App.tsx` — Paylaşılan bileşenler (`Btn`, `ProgressBar`, `CpiGauge`, `MetricCard`, `Card`, `CardHeader`, `PageHeader`, `ChartTooltipContent`) artık her zaman açık tema kullanıyor
- `main` arka planındaki `screen === "dashboard"` koşulu kaldırıldı; koşulsuz `bg-[#f4f7fb]` yapıldı
- 13 ekranın tamamında koyu tablo/kart/chart renkleri açık tema eşdeğerleriyle değiştirildi:
  - Portfolio, Budget, EVM, Variance, Hakedis, SCurve, CashFlow, Report, DataUpload, Users, Glossary, ResourceList
- Chart `CartesianGrid stroke` ve `XAxis/YAxis tick` renkleri `#e2e8f0` ve `#64748b` yapıldı
- Toggle butonlar (`border-[#1e2a3d]` → `border-slate-200`), tablo footer'ları açık yapıldı
- Inline CV/CPI/sapma renkleri `#34d399` → `#059669`, `#f87171` → `#dc2626` olarak güncellendi

**Build sonucu:** ✓ `npm run build` başarılı (2026-06-28, 6.30s, 673 kB)

### LOOP-02 — Grafik ve Arama Kutusu Tutarlılığı (Sıradaki)

Tüm ekranlarda `CartesianGrid`, `Legend`, `Tooltip` renklerinin `#64748b` / `#e2e8f0` ile tutarlı olduğunu doğrula. ResourceList arama kutusu ve Glossary performans tablosu renk bütünlüğü sağla.

### LOOP-03 — Component Refactor (Sonraki Aşama)

`light` prop artık gerekmiyor; eski kodu temizle. `App.tsx` ekranlarını ayrı dosyalara bölme planı hazırla.
