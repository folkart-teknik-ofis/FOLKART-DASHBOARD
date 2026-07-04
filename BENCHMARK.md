# BENCHMARK

Bu belgede, Phase 2 kapsamında geliştirilecek bütçe ve maliyet modülleri için referans alınacak endüstri lideri ürünlerin iş akışı ve veri modelleri analiz edilmiştir.

---

## 1. LivaBudget

### Bütçe Yönetimi Mantığı
*   İnşaat projelerine özel geliştirilmiş, çok seviyeli WBS yapısını ve bütçe kırılımlarını destekleyen yerli bütçe yazılımı.
*   Bütçe versiyonlama (Keşif Bütçesi, Master Bütçe, Hedef Bütçe) ve revizyon takibi esastır.

### Maliyet Takip Mantığı
*   İş kalemlerine bağlanan kaynak kodları üzerinden anlık gerçekleşen maliyet takibi.
*   SAP entegrasyonu ile muhasebeleşen fiili maliyetleri eşleştirebilme.

### Hakediş / Payment Application Mantığı
*   Yüklenici hakedişlerinin WBS düzeyinde metraj ve birim fiyat detayında (icmal formatında) sisteme girilmesi ve onay akışları.

### Sözleşme Bağlantısı
*   Taşeron sözleşmeleri ile WBS bütçe kalemlerinin birebir ilişkilendirilmesi; sözleşme bütçesinin master bütçeyi aşım kontrolleri.

### Nakit Akış / Forecast Mantığı
*   Bütçe kalemlerinin ödeme vadeleri, KDV oranları ve avans mahsupları girilerek otomatik üretilen TL/USD nakit akış tahminleri.

### Veri Modeli Açısından Alınabilecek Fikirler
*   Bütçe satırlarında orijinal para birimi tutulması ve günlük/aylık kur tablolarıyla kümülatif raporlamalarda kur dönüşümü yapılması.
*   Kaynak Kodu -> Alt PYP -> Ana PYP -> Bütçe Kalemi hiyerarşik veri modeli.

### UI / Dashboard Açısından Alınabilecek Fikirler
*   Aylık katsayı tanımlama matrisi (Kur, enflasyon ve emtia artış katsayılarının girildiği ızgara görünümü).
*   Gelişmiş WBS ağaç görünümünde planlanan ve gerçekleşen maliyetlerin yan yana listelendiği geniş tablolar.

### Bizim Projeye Uyarlanabilecek Sade Karşılığı
*   Kullanıcının bütçe satırı bazında kur, enflasyon, inşaat endeksi ve emtia çarpanlarından bir veya birkaçını seçip uygulayabildiği dinamik katsayı matrisi.

### Kopyalanmayacak / Kapsam Dışı Noktalar
*   SAP ile doğrudan iki yönlü anlık veri senkronizasyonu ve ERP onay mekanizmaları (Phase 2 dışındadır).

---

## 2. Procore Project Financials

### Bütçe Yönetimi Mantığı
*   Proje bütçelerinin iş kodları (Cost Codes) bazında satır satır yönetildiği, esnek kolon yapısına sahip bütçe modülü.
*   Bütçe değişiklik talepleri (Budget Modifications) ile bütçe transferleri izlenebilir.

### Maliyet Takip Mantığı
*   Taahhütler (Commitments) ve doğrudan maliyetler (Direct Costs) üzerinden gerçekleşen maliyet takibi.

### Hakediş / Payment Application Mantığı
*   Taşeronlardan gelen aylık ilerleme faturalarının (Invoices) ve hakedişlerin WBS kırılımında onaylanması.

### Sözleşme Bağlantısı
*   Sözleşmeler (Prime Contracts, Subcontracts) ve bunlara bağlı Değişiklik Siparişleri (Change Orders) bütçe kolonlarında ayrı izlenir.

### Nakit Akış / Forecast Mantığı
*   Kazanılmış değer (EVM) ve manuel forecast tahminleriyle Tahmini Nihai Maliyet (EAC) hesaplama.

### Veri Modeli Açısından Alınabilecek Fikirler
*   Her bütçe satırında: Orijinal Bütçe + Onaylı Revizyonlar + Bekleyen Revizyonlar = Güncel Bütçe veri ilişkisi.

### UI / Dashboard Açısından Alınabilecek Fikirler
*   Dashboard üzerinde sapmaları kırmızı/yeşil durum göstergeleri (indikasyonlar) ile görselleştirme.

### Bizim Projeye Uyarlanabilecek Sade Karşılığı
*   Hakedişlerin genel tutar yerine, doğrudan WBS (PYP) kalemleri altında metraj bazında girilerek birikimli ödeme takibinin yapılması.

### Kopyalanmayacak / Kapsam Dışı Noktalar
*   Satınalma, teklif toplama, fatura onay akışları ve fatura görüntüleme entegrasyonları.

---

## 3. Oracle Primavera Unifier Cost / Contract Management

### Bütçe Yönetimi Mantığı
*   Çok katı kurallara sahip, kurumsal düzeyde CBS (Cost Breakdown Structure) bazlı bütçe yönetimi.
*   İş akışları (Workflows) üzerinden bütçe onaylama süreçleri yürütülür.

### Maliyet Takip Mantığı
*   Unifier CBS kodları ile ERP/Oracle e-Business Suite kodlarının entegre takibi.

### Hakediş / Payment Application Mantığı
*   İlerime hakedişlerinin hakediş icmali, avans mahsubu, teminat kesintileri ve ceza kesintileri detayında izlenmesi.

### Sözleşme Bağlantısı
*   Sözleşme yönetimi, sözleşme zeyilnameleri (Amendments) ve bunlara bağlı hakediş limit denetimleri.

### Nakit Akış / Forecast Mantığı
*   S-Eğrileri ile entegre, nakit çıkış vadelerine göre dağıtılmış nakit akış planlama.

### Veri Modeli Açısından Alınabilecek Fikirler
*   Hakediş icmal tablosunda avans ödemesi ve avans mahsubunun kümülatif bakiyelerinin ayrı birer veri sütunu olarak tutulması.

### UI / Dashboard Açısından Alınabilecek Fikirler
*   Gelişmiş bütçe ve nakit akış S-Eğrisi grafiklerinin üst üste bindirilmiş (Composed) gösterimleri.

### Bizim Projeye Uyarlanabilecek Sade Karşılığı
*   Nakit akışında; avans ödemesi, avans mahsubu, KDV, kesintiler ve net nakit çıkışının satır düzeyinde ayrıştırılarak TL nakit akış raporu üretilmesi.

### Kopyalanmayacak / Kapsam Dışı Noktalar
*    Primavera P6 entegrasyonu, Unifier iş akışı tasarım editörü, kurumsal seviyede esnek CBS şema tanımlayıcıları.

---

## 4. Autodesk Construction Cloud Cost Management

### Bütçe Yönetimi Mantığı
*   Bütçe satırlarının kontratlarla ve harcamalarla ilişkisini gösteren görsel "Bütçe Tablosu" (Budget Grid).
*   Görsel olarak bütçe transferlerini sürükle-bırak veya sihirbazlar ile yapabilme.

### Maliyet Takip Mantığı
*   Gerçekleşen giderlerin (Expenses) ve taşeron faturalarının iş kalemlerine yansıtılması.

### Hakediş / Payment Application Mantığı
*   Aylık yüklenici hakediş başvurularının ve ödeme onaylarının WBS satırlarıyla görsel eşleşmesi.

### Sözleşme Bağlantısı
*   Sözleşme bütçesi ile ana proje bütçesinin sapma analizi (Variance Analysis).

### Nakit Akış / Forecast Mantığı
*   Bütçe dağılım eğrileri (Örn: lineer, çan eğrisi, ön ağırlıklı) seçilerek aylık otomatik forecast üretimi.

### Veri Modeli Açısından Alınabilecek Fikirler
*   Bütçe dağıtım yöntemi (Distribution Method: Lineer, Çan Eğrisi, Ön Ağırlıklı, Arka Ağırlıklı) seçiminin bütçe versiyon modelinde tutulması.

### UI / Dashboard Açısından Alınabilecek Fikirler
*   Bütçe satırlarının durumuna göre renklendirilmiş durum çubukları ve etkileşimli WBS tabloları.

### Bizim Projeye Uyarlanabilecek Sade Karşılığı
*   Bütçe sihirbazında kullanıcıya "Dağıtım Yöntemi" (Lineer, Çan Eğrisi, Manuel vb.) sorularak aylık PV (Planlanan Değer) serilerinin buna göre otomatik üretilmesi.

### Kopyalanmayacak / Kapsam Dışı Noktalar
*   Autodesk Docs belge yönetim sistemi entegrasyonu, BIM 360 entegrasyonları, 3D model üzerinden metraj çıkarma modülleri.
