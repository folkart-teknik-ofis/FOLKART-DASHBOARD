# DATA_MODEL_ANALYSIS

## 1. Mevcut Veri Tipleri ve Model Yapıları

Uygulamanın veri modeli [`src/app/types.ts`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/types.ts) dosyasında tanımlanan arayüzlerden (interfaces) oluşmaktadır:

### A. Proje Arayüzü (`Project`)
Projelerin genel künye ve finansal özet bilgilerini tutar:
```typescript
export interface Project {
  id: string;                      // "P001" (Benzersiz ID)
  projectCode: string;             // "3022" (Folkart Proje Kodu)
  projectName: string;             // "Nova" (Proje Adı)
  projectType: string;             // "Karma Proje", "Konut Projesi" vb.
  currentMonth: number;            // Mevcut ay numarası (örn: 18)
  totalDurationMonth: number;      // Proje toplam süresi ay bazında (örn: 36)
  physicalProgress: number;        // Fiziki ilerleme yüzdesi (örn: 67)
  physPct: number;                 // physicalProgress ile uyumluluk takma adı (alias)
  landArea: number;                // Arsa alanı m²
  constructionArea: number;        // İnşaat alanı m²
  sellableArea: number;            // Satılabilir alan m²
  floorCount: number;              // Kat sayısı
  residentialUnitCount: number;    // Konut adedi
  officeUnitCount: number;         // Ofis adedi
  commercialUnitCount: number;     // Ticari ünite adedi
  costPerM2: number;               // m² birim maliyeti
  startDate: string;               // Başlangıç tarihi (YYYY-MM-DD)
  plannedEndDate: string;          // Planlanan bitiş tarihi (YYYY-MM-DD)
  currentEndDate: string;          // Güncel bitiş tarihi (YYYY-MM-DD)
  currency: string;                // Para birimi (örn: "TRY")
  status: string;                  // Durum (active, onhold, completed, planning)
  manager: string;                 // Proje müdürü adı
  description: string;             // Açıklama metni
  activeBudgetVersionId: string;   // Projenin o an aktif olan bütçe versiyonunun ID'si
  bac: number;                     // Bütçe BAC (Milyon TL bazında)
  ac: number;                      // Gerçekleşen maliyet AC (Milyon TL bazında)
  ev: number;                      // Kazanılmış değer EV (Milyon TL bazında)
  eac: number;                     // Tahmini nihai maliyet EAC (Milyon TL bazında)
  timePct: number;                 // Geçen süre yüzdesi (%)
  pm: string;                      // Proje müdürü adı (manager ile aynı)
  start: string;                   // Başlangıç yılı-ayı (YYYY-MM)
  end: string;                     // Bitiş yılı-ayı (YYYY-MM)
  contract: string;                // Sözleşme tipi (örn: "KDV Dahil Götürü")
}
```

### B. Bütçe Versiyon Arayüzü (`BudgetVersion`)
```typescript
export interface BudgetVersion {
  budgetVersionId: string;         // Versiyon benzersiz ID'si
  projectId: string;               // İlgili Proje ID'si
  versionType: "Fizibilite" | "Keşif" | "Master"; // Versiyon kategorisi
  versionNo: string;               // Versiyon numarası (örn: "M1")
  versionCode: string;             // Versiyon kodu (örn: "M1")
  isActive: boolean;               // Aktiflik durumu
}
```

### C. WBS Finansal Değer Arayüzü (`ProjectWbsValue`)
Excel import ve EVM hesaplarının temel veri noktası bu arayüzdür. Her bütçe satırı bazında finansal değerleri tutar:
```typescript
export interface ProjectWbsValue {
  projectWbsValueId: string;       // "P001-V001-09.02.01" gibi benzersiz anahtar
  projectId: string;               // İlgili Proje ID'si
  budgetVersionId: string;         // İlgili Bütçe Versiyon ID'si
  kaynakKodu: string;              // "09.02.01" gibi kaynak kodu
  bac: number;                     // Bütçe Tutarı (Milyon TL)
  pv: number;                      // Planlanan Değer (Milyon TL)
  ev: number;                      // Kazanılmış Değer (Milyon TL)
  ac: number;                      // Gerçekleşen Maliyet (Milyon TL)
  eac: number;                     // Tahmini Nihai Maliyet (Milyon TL)
  hakedis: number;                 // Hakediş Tutarı
  nakitPlan: number;               // Planlanan nakit çıkışı
  nakitGerceklesen: number;        // Gerçekleşen nakit çıkışı
}
```

### D. WBS Hiyerarşi Görünüm Arayüzü (`WBSFinanceItem`)
Bütçe ve EVM ekranlarında hiyerarşik satırları hesaplayıp göstermek için dinamik olarak üretilir:
```typescript
export interface WBSFinanceItem {
  anaPyp: string;                  // "01" (Ana PYP Kodu)
  anaPypAdi: string;               // "Şantiye Genel Giderler"
  altPyp: string;                  // "01.01" (Alt PYP Kodu)
  altPypAdi: string;               // "Şantiye Personel Gideri"
  kod: string;                     // "01.01.01" (Kaynak Kodu)
  ad: string;                      // "Kıdem Tazminatı"
  birim: string;                   // "Tutar", "Lt", "m3" vb.
  bac: number;
  pv: number;
  ev: number;
  ac: number;
  cv: number;
  cvPct: number | null;
  sv: number;
  svPct: number | null;
  cpi: number | null;
  spi: number | null;
  status: "green" | "yellow" | "red";
}
```

---

## 2. Mevcut WBS / PYP Yapısı ve Hiyerarşi Mantığı

Uygulamada standart hiyerarşi **Kaynak Kodu → Alt PYP → Ana PYP → Proje** şeklindedir:
*   **Ana PYP (Düzey 0)**: Kaynak kodunun ilk 2 hanesidir. Örn: `01` - Şantiye Genel Giderler, `09` - Kaba Yapı İşleri.
*   **Alt PYP (Düzey 1)**: Kaynak kodunun ilk 5 hanesidir. Örn: `01.01` - Şantiye Personel Gideri, `09.02` - Hafriyat İşleri.
*   **Kaynak Kodu (Düzey 2)**: 8 haneli tam koddur. Örn: `01.01.01` - Kıdem Tazminatı, `09.02.01` - Kazı ve Hafriyat.

Bütçe değerlerinin (`ProjectWbsValue`) tablolarda kümülatif toplanması bu kod hiyerarşisine dayanır. Üst kırılımların (Ana PYP ve Alt PYP) BAC/PV/EV/AC değerleri, altındaki Kaynak Kodlarının değerlerinin toplanmasıyla elde edilir.

**Phase 2 Kaynak Kodu Zorunluluğu İptali ve Esnek Seviye Takibi**:
Kaynak kodu zorunlu değildir ve sistem otomatik sanal kaynak kodu üretmeyecektir. Gelen bütçe satırı hangi seviyedeyse o seviyede takip edilecektir:
*   Kaynak Kodu varsa Kaynak Kodu seviyesinde
*   Kaynak Kodu yoksa Alt PYP seviyesinde
*   Alt PYP de yoksa Ana PYP seviyesinde
Eksik kaynak kodu nedeniyle bütçe satırları reddedilmeyecek, ancak kullanıcıya "bu satır kaynak kodu olmadan Alt PYP / Ana PYP seviyesinde takip ediliyor" şeklinde bilgi/uyarı verilecektir.

---

## 3. Planlanan ve Gerçekleşen Veri İlişkisi (EVM)

*   **BAC (Bütçe)**: Proje veya kalemin toplam onaylı bütçesidir.
*   **PV (Planlanan Değer)**: Seçilen rapor ayına kadar planlanan kümülatif bütçedir. Ay bazlı plan dağılım katsayıları (`MONTHLY_CF`) kullanılarak hesaplanır.
*   **AC (Gerçekleşen Maliyet)**: Dönem itibarıyla fiilen yapılan harcamadır.
*   **EV (Kazanılmış Değer)**: Yapılan fiziki ilerlemenin bütçesel karşılığıdır (`BAC * Fiziki İlerleme %`).

**Phase 2 PV/AC/BAC Kontrol Kuralları**:
*   Geçmiş ay kilitlendiğinde PV hiçbir zaman AC'ye eşitlenmeyecek; planlanan değer planlanan olarak kalacak, gerçekleşen gerçekleşen olarak kalacak ve sapma korunacaktır.
*   Kümülatif PV, BAC üzerinden hesaplanan planlanan ilerlemeyi gösterir.
*   Kümülatif AC, gerçekleşen maliyeti gösterir.
*   Maliyet gerçekleşme oranı kümülatif AC / BAC üzerinden kontrol edilecektir.
*   PV, AC ve BAC birbirine karıştırılmayacaktır. EVM hesaplarında mevcut formüller bozulmadan, kullanılan oranların doğru isimlendirildiği kontrol edilecektir.

---

## 4. Excel Veri Normalizasyonu

`DataUploadScreen` üzerinde WBS Excel dosyası yüklendiğinde şu normalizasyon adımları izlenir:
1.  **Satır Ayrıştırma**: XLSX satırları JSON formatına çevrilir.
2.  **Alan Eşleştirme**: Türkçe karakter ve harf büyüklüğüne duyarlı şekilde `"Kaynak Kodu"`, `"Planlanan Miktar"`, `"Planlanan Birim Fiyat"`, `"Birim"` kolonları okunur.
3.  **Tutar Dönüştürme**: `Planlanan Miktar * Planlanan Birim Fiyat` çarpımı yapılıp `1,000,000`'a bölünerek sistemin standart para birimi olan **Milyon TL (M₺)** birimine normalize edilir.
4.  **Anahtarlama**: Eğer satırdaki `kaynak_kodu` daha önce varsa eski kayıt ezilir, yoksa otomatik olarak sonuna `_auto` eklenen yeni bir Wbs değeri oluşturulur.

---

## 5. Eksik / Tasarlanması Gereken Veri Modelleri (Phase 2)

İlk fazda statik veya mock bırakılmış olan, Phase 2'de ortak veri modeline bağlanması gereken alanlar şunlardır:

1.  **Gelişmiş Bütçe Tipi Modeli (Fizibilite / Keşif / Master / Revize)**: Sadece `versionType` stringi mevcuttur. Bütçe onay akışı, onaylayan kişi ve onay tarihi gibi üst verilerin tutulacağı bir üst bütçe modeli eksiktir.
2.  **Hakediş İcmal İlişkisi**: Hakediş tablosunda sadece genel tutar tutulmaktadır. Hakedişin hangi PYP / Alt PYP / Kaynak Kodu kalemlerine yapıldığını, hakediş birim fiyatını ve metraj detaylarını içeren icmal alt tablosu bulunmamaktadır.
3.  **Avans ve Avans Mahsubu Veri Yapısı**: Avans ödemelerinin tutulacağı veri alanı, hakediş dönemlerine göre yapılacak avans mahsup oranları/tutarları modelde yoktur.
4.  **Çarpan ve Varsayım Tabloları**: Aylık bazda kur tahminleri, enflasyon oranları, inşaat endeksi çarpanları ve emtia katsayılarının proje bazında tutulacağı tablolar eksiktir.
5.  **Çoklu Para Birimi & Dönüşüm Kayıtları**: Bütçe kalemlerinin orijinal para birimi (`USD`, `EUR`, `TRY`), bütçe tarihindeki orijinal kur değeri, rapor tarihindeki güncel kur değeri ve raporlama para birimi karşılığı satır düzeyinde tutulmamaktadır.
6.  **Nakit Akış Modeli**: Nakit akış hesaplarında ödeme vadeleri (vade öteleme), kesintiler (gelir vergisi, teminat kesintisi vb.) ve KDV oranlarının tutulacağı alanlar eksiktir.

**Veri Yükleme Ekranı Genişletme Alanları (Girdi/Excel Tabloları)**:
Phase 2'de Data Upload ekranı üzerinden ay/yıl bazında girilebilecek veya yüklenebilecek yeni veri grubu tabloları şunlardır:
*   WBS bütçe
*   Gerçekleşen maliyet
*   Hakediş icmali
*   Kur tabloları (Planlanan USD kuru, Planlanan EUR kuru, Gerçekleşen USD kuru, Gerçekleşen EUR kuru)
*   Enflasyon tabloları (TL enflasyonu, USD enflasyonu, EUR enflasyonu)
*   İnşaat endeksi tabloları
*   Emtia katsayı tabloları
*   Avans / kesinti tabloları

---

## 6. Geliştirme Önerileri ve Koruma Planı

### Korunacak Veri Yapıları
*   `types.ts` içerisindeki `Project` ve `ProjectWbsValue` yapıları bozulmamalıdır. Dashboard ekranı ve EVM motoru bu yapılara sıkı sıkıya bağlıdır.
*   `RESOURCE_LIST` hiyerarşik kırılım modeli korunmalıdır.

### Eklenecek Veri Yapıları
*   `ExchangeRatesTable`: Proje bazında aylık planlanan ve gerçekleşen USD/EUR kur serilerini tutan tablo.
*   `BudgetMultipliers`: Bütçe satırlarında hangi çarpanların (enflasyon, kur, emtia) aktif olduğunu belirten bayraklar (flags).
*   `HakedisIcmal`: Hakediş numarasıyla ilişkili alt kırılımlı metraj ve tutar tablosu.
*   `NakitAkisAyarlari`: Ödeme vadesi (ay bazında), avans oranı, avans mahsup oranı, teminat kesinti oranlarını içeren bütçe parametre modeli.
