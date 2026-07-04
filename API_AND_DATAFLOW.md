# API_AND_DATAFLOW

Bu belgede Folkart Bütçe ve Maliyet Yönetim Platformu Phase 2 veri entegrasyonu yol haritası (Excel, SQL, LivaBudget, SAP) ve sistem genelinde netleştirilmesi gereken eksik iş kuralları ile çelişkiler tanımlanmıştır.

---

## 1. Veri Entegrasyonu Aşamaları (Roadmap)

### A. Birinci Faz: Excel İçe Aktarım (Excel Import)
İlk aşamada, çevrimdışı (offline) veri aktarımı için Excel dosyaları kullanılacaktır.

```
[Kullanıcı Excel Dosyası]
         │
         ▼ (Drag & Drop / Dosya Seçimi)
[DataUploadScreen.tsx (İstemci)]
         │
         ▼ (SheetJS / FileReader ile Okuma)
[Raw JSON Satırları]
         │
         ▼ (Kolon & Tip Doğrulaması)
[Bütçe Tutarı Normalizasyonu (Tutar / 1.000.000)]
         │
         ▼ (React State Güncellemesi)
[projectWbsValues State]
```

*   **Veri Kaynağı**: `.xlsx` veya `.xls` şablonları.
*   **İşlem Yeri**: Tamamen istemci tarafı (client-side).
*   **Doğrulama**: Gerekli kolonların (`Kaynak Kodu`, `Planlanan Miktar` vb.) varlığı ve veri tipleri kontrol edilir.

---

### B. İkinci Faz: SQL / Veri Havuzu Bağlantısı (Database Integration)
Uygulamanın sunucu tabanlı bir mimariye geçiş aşamasıdır.

```
[İstemci / Tarayıcı (React App)]
         │
         ▼ (HTTPS / REST API - Axios/Fetch)
[API Sunucusu (Node.js / Express veya .NET Core)]
         │
         ▼ (ORM - Prisma / Entity Framework)
[İlişkisel Veritabanı (PostgreSQL / SQL Server)]
```

*   **Akış**: İstemci, proje veya dönem değiştirdiğinde API üzerinden asenkron istek (`GET /api/projects/:id/wbs`) gönderir. Excel yüklemesi yapıldığında veri `POST /api/wbs/import` rotasıyla veritabanına kalıcı olarak yazılır.
*   **Doğrulama**: Veritabanı şema kısıtları, benzersiz anahtar (`Unique Key`) kontrolleri ve ilişkisel bütünlük (`Foreign Key`) denetimleri sunucu tarafında yapılır.

---

### C. Üçüncü Faz: LivaBudget Entegrasyonu
Bütçe ve planlama verilerinin LivaBudget yazılımından otomatik senkronizasyonudur.

```
[LivaBudget API / Veritabanı]
         │
         ▼ (Zamanlanmış ETL Görevi veya Webhook)
[Bizim API Sunucumuz (Normalizasyon Servisi)]
         │
         ▼ (WBS Hiyerarşisi Eşleştirme)
[PostgreSQL / SQL Server (Bizim Veritabanımız)]
```

*   **Akış**: LivaBudget üzerindeki onaylı bütçe versiyonları ve varsayımlar, geliştireceğimiz bir entegrasyon servisi (ETL) aracılığıyla okunur. Liva WBS yapısı, bizim 3 seviyeli standardımıza dönüştürülerek veritabanına yazılır.

---

### D. Dördüncü Faz: SAP Entegrasyonu
Gerçekleşen maliyetlerin (`AC`) ve fatura kayıtlarının SAP FI/CO modüllerinden çekilmesidir.

```
[SAP RFC / REST Gateway]
         │
         ▼ (Çift Yönlü Güvenli API / Data Lake View)
[Bizim API Sunucumuz (SAP Eşleştirme Motoru)]
         │
         ▼ (Kaynak Kodu bazında AC Dağıtımı)
[ActualCosts Tablosu (Bizim Veritabanımız)]
```

*   **Akış**: SAP üzerinde onaylanan taşeron hakedişleri ve kesilen faturalar, SAP entegrasyon servisimiz aracılığıyla periyodik olarak (günlük/haftalık) çekilir. SAP masraf merkezleri ve hesap kodları, bizim Kaynak Kodlarımızla eşleştirilerek `ActualCosts` tablosuna gerçekleşen maliyet (`AC`) olarak kaydedilir.

---

## 2. Eksik İş Kuralları ve Çelişkiler

Phase 2 geliştirmelerine başlamadan önce ürün sahibi (product owner) ile netleştirilmesi ve çözülmesi gereken kritik iş kuralları ile tasarım çelişkileri şunlardır:

### 1. Kaynak Kodu Olmama Çelişkisi
*   **Tanım**: Dokümanda "Kaynak kodu zorunlu olmayacaktır. Sistem bütçe girişini üç seviyede destekleyecektir: Kaynak kodu, Alt PYP, Ana PYP bazlı" denmektedir.
*   **Çelişki/Açık**: Mevcut WBS yapısı ve kümülatif EVM toplama motoru, verilerin her zaman en alt seviye olan `Kaynak Kodu` (8 haneli benzersiz kod) bazında yüklenmesi ve üst kırılımların bu kodlar üzerinden toplanması esasına dayanmaktadır. Eğer bir bütçe sadece Alt PYP bazlı girilirse, o satırın Kaynak Kodu boş mu kalacaktır? Boş kalırsa sistem bunu WBS tablosunda tekil olarak nasıl tanımlayacak? 
*   *Öneri*: Kaynak kodu olmayan durumlar için sistem arka planda sanal bir genel kaynak kodu (Örn: Alt PYP Kodu + `.99` ➔ `09.02.99`) üretmeli midir?

### 2. Çoklu Para Birimi ve Kur Çevrim Kuralları
*   **Tanım**: Bütçe satırlarının karışık para birimli (TRY, USD, EUR) gelebileceği ve nakit akışının TL üretileceği belirtilmiştir.
*   **Eksiklik**: Kur dönüşümü yapılırken hangi tarihin kuru baz alınacaktır?
    *   *Senaryo A*: Sabit Bütçe Kuru (Bütçenin onaylandığı günkü kur - planlanan ve gerçekleşen için aynı kalır).
    *   *Senaryo B*: Dinamik Kur (Planlanan maliyet için o ayın kur çarpanı tablosundaki tahmini kur; gerçekleşen maliyet için fatura tarihindeki fiili kur).
*   Kur farkından kaynaklanan sapmalar EVM raporlarında "Maliyet Sapması" (`CV`) olarak mı gösterilecek yoksa "Kur Sapması" adıyla ayrı bir kalemde mi raporlanacaktır?

### 3. KDV Hesaplama Matrahı
*   **Tanım**: Hesaplama formülü olarak: `Kesinti sonrası tutar = TL karşılığı - seçili kesintiler` ve `KDV dahil tutar = Kesinti sonrası tutar + KDV` verilmiştir.
*   **Çelişki/Açık**: İnşaat hakediş mevzuatlarında KDV, kesintiler düşülmeden önceki **Brüt Hakediş Tutarı** üzerinden hesaplanır. Formüldeki gibi kesintiler düşüldükten sonra KDV hesaplanması yasal faturalama süreçleriyle çelişebilir. KDV matrahının brüt tutar mı yoksa kesinti sonrası tutar mı olacağı kesinleştirilmelidir.

### 4. Geçmiş Dönemlerin Kilitlenmesi ve ETC Dağıtımı
*   **Tanım**: Gerçekleşenler geldikçe geçmiş dönemler kilitlenecektir.
*   **Eksiklik**: Geçmiş dönem kilitlendiğinde, o döneme ait bütçelenmiş planlanan değer (`PV`), fiili gerçekleşen değer (`AC`) ile eşitlenip sapma sıfırlanacak mıdır? Yoksa orijinal `PV` korunup sapma tarihsel olarak kalacak mıdır? 
*   Kalan bütçe (ETC) hesaplandıktan sonra, önümüzdeki aylara nasıl dağıtılacaktır? Yeniden planlama lineer mi yapılacak yoksa kullanıcı tarafından manuel mi girilecektir?

### 5. Avans Mahsup Sınırları ve Negatif Hakediş Riski
*   **Tanım**: Avans ödemelerinin nakit akışından düşülmesi (mahsup edilmesi) süreci.
*   **Eksiklik**: Eğer bir ay yapılan brüt hakediş tutarı, o ay yapılacak avans mahsubundan ve teminat kesintilerinden daha düşük kalırsa (negatif ödeme tutarı oluşursa) nakit akışında bu durum nasıl gösterilecektir? Avans mahsup oranı hakedişin en fazla yüzde kaçına kadar uygulanabilir? Avans tamamen mahsup edilmeden sözleşme feshedilirse kalan bakiye bütçeye nasıl yansıtılacaktır?
