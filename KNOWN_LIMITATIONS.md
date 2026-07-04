# KNOWN_LIMITATIONS

## 1. Mevcut Sistem Yetenekleri (Neleri Yapabiliyor?)

*   **EVM Hesaplamaları**: PMBOK uyumlu temel kazanılmış değer formüllerini (CV, SV, CPI, SPI, EAC, ETC, VAC, TCPI) hatasız çalıştırabiliyor.
*   **WBS Hiyerarşisi**: Ana PYP -> Alt PYP -> Kaynak Kodu şeklinde 3 seviyeli bütçe ağacı oluşturup kümülatif toplamları yansıtabiliyor.
*   **Excel Yükleme**: WBS şablonundaki planlanan miktarları ve birim fiyatları okuyarak bütçeyi dinamik olarak güncelleyebiliyor.
*   **Proje Yönetimi**: Portföy listesine yeni proje (künye bilgileriyle) ekleyebiliyor ve durum takibi yapabiliyor.
*   **Dönem Ölçekleme**: Seçilen rapor ayının kümülatif ilerleme katsayısına göre planlanan ve gerçekleşen değerleri dinamik olarak ölçekleyip geçmiş ayları simüle edebiliyor.

---

## 2. Mevcut Sistem Sınırları (Neleri Yapamıyor?)

*   **Veri Kalıcılığı**: Veriler sadece tarayıcı belleğinde (state) tutulur; sayfa yenilendiğinde Excel'den yüklenen veya el ile eklenen tüm projeler sıfırlanır (Localstorage üzerinde sadece yükleme geçmişi logları saklanır).
*   **Dinamik Grafik Bağlantısı**: S-Eğrisi, Nakit Akışı ve EVM Trend grafikleri Excel'den yüklenen gerçek verileri değil, `mockData.ts` içindeki statik veri serilerini gösterir.
*   **Para Birimi Kısıtı**: Sistem tüm bütçe verilerini tek bir para birimi (varsayılan TL) kabul eder. Orijinal para birimi bazında kayıt tutamaz veya anlık kur çevrimi yapamaz.
*   **Hakediş İcmali Yok**: Hakedişler sadece toplu tutar bazında takip edilir; WBS kalemleriyle, metrajlarla ve birim fiyatlarla ilişkilendirilemez.
*   **Çarpan ve Katsayı Yönetimi Yok**: Bütçe satırlarına uygulanabilecek enflasyon, kur çarpanı veya emtia katsayısı tabloları ve hesaplama mekanizmaları mevcut değildir.
*   **Avans ve Kesinti Hesabı Yok**: Nakit akış hesaplarında avans ödemesi, avans mahsubu, KDV ve teminat kesintileri hesaba katılmaz.
*   **Ödeme Vadesi Öteleme**: Maliyet planı ile nakit akış planı aynı takvim ayında gösterilir; vade ötelemesi (örn: 30 gün sonra ödeme) yapılamaz.

---

## 3. Excel İçe Aktarım Sınırları

*   Hassas kolon adı eşleşmesi gerektirir. Sütun isimlerinde yapılacak en ufak bir harf veya karakter değişimi dosyanın okunmasını engeller.
*   Yalnızca WBS/Planlanan değerleri yükleyebilir. Gerçekleşen maliyetlerin (`gerceklesen`) ve hakedişlerin (`hakedis`) Excel şablonları indirilebilir olsa da, bunları içeri aktaracak işlevsel bir kod yazılmamıştır (sadece dosya yükleme geçmişine ekler, veritabanını güncellemez).

---

## 4. SQL / Veri Havuzu Entegrasyonu Hazırlık Durumu

*   Uygulama tamamen mock veriye dayalı istemci tarafı (client-side) mimarisindedir.
*   Herhangi bir API çağrısı veya veri katmanı soyutlaması (Data Access Layer) yoktur.
*   SQL veya veri havuzu entegrasyonu için öncelikle tüm veri çekme ve kaydetme işlemlerinin asenkron API servislerine (Fetch/Axios) taşınması gerekmektedir. Mevcut kod tabanı doğrudan SQL entegrasyonuna hazır değildir.

---

## 5. Phase 2 İçin En Kritik Riskler

1.  **Dashboard Hesaplamalarının Bozulması**: Mevcut dashboard ve EVM motoru (`evmEngine.ts`) tek para birimi ve belirli veri tiplerine göre çalışmaktadır. Phase 2'de çoklu para birimi ve kurlar eklendiğinde, kümülatif toplama mantığı bozulabilir ve dashboard metriklerinde yanlışlıklar oluşabilir.
2.  **State Yönetimi Karmaşası**: ~3368 satırlık monolitik `App.tsx` yapısı yeni eklenecek olan bütçe, nakit akışı ve varsayım ekranlarıyla birlikte daha da büyüyecek ve okunamaz/bakımı yapılamaz hale gelecektir.
3.  **Excel Import Çakışmaları**: Mevcut Excel import mantığı doğrudan bütçe state'ini günceller. Çoklu bütçe versiyonları (Master, Revize vb.) devreye girdiğinde, Excel'in hangi versiyonu güncelleyeceğinin seçimi iyi yönetilmezse verilerin üst üste yazılması riski vardır.
4.  **Performans Düşüşü**: İstemci tarafında çalışan dinamik WBS ağaç hesaplamaları, veri satır sayısı arttıkça ve çarpanlar (enflasyon, kur, emtia) devreye girdikçe tarayıcıda yavaşlamalara neden olabilir.
5.  **Firebase Build Hataları**: Projede Tailwind v4 derleme ve Vite chunk boyutu limitleri mevcuttur. Çok sayıda yeni kütüphane veya modül eklenmesi, production build çıktısının bozulmasına veya şişmesine neden olabilir.
