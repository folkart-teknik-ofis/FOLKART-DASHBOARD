# PRODUCT_VISION_PHASE_2

## 1. Ürün Vizyonu
Bu proje mevcut EVM dashboard sistemini bozmadan yaşayan bir Bütçe ve Maliyet Yönetim Platformu haline getirilecektir.

## 2. Ana Kural
Mevcut Phase 1 proje korunacak.
Phase 2 geliştirmeleri duplicate proje üzerinde yapılacak.
Mevcut çalışan EVM, WBS, PYP, Alt PYP, kaynak kodu, Excel import, dashboard ve grafik yapısı bozulmayacak.

## 3. Kapsam
- Bütçe oluşturma
- Keşif bütçesi
- Master bütçe
- Revize bütçe
- Maliyet takibi
- Hakediş icmal takibi
- Planlanan / gerçekleşen / kalan bütçe
- Çarpanlı nakit akış
- Avans nakit akışı
- Dashboard ve rapor çıktıları

## 4. Kapsam Dışı
- Satınalma modülü
- Risk modülü
- Monte Carlo
- Hedge
- Primavera entegrasyonu
- Doğrudan SAP entegrasyonu
- ERP seviyesinde tüm süreçlerin tek seferde yapılması

## 5. Mevcut WBS / PYP / Alt PYP / Kaynak Kodu Yapısı
Standart hiyerarşi:
Kaynak Kodu → Alt PYP → Ana PYP → Proje

Ancak kaynak kodu zorunlu olmayacaktır.
Sistem bütçe girişini ve takibini üç seviyede destekleyecektir:
- Kaynak Kodu varsa Kaynak Kodu seviyesinde
- Kaynak Kodu yoksa Alt PYP seviyesinde
- Alt PYP de yoksa Ana PYP seviyesinde

Sistem eksik kaynak kodu nedeniyle bütçe satırını reddetmeyecek, sanal kaynak kodu üretmeyecektir. Satır hangi seviyedeyse o seviyede takip edilecektir. Ancak kullanıcıya “bu satır kaynak kodu olmadan Alt PYP / Ana PYP seviyesinde takip ediliyor” şeklinde bilgi verilecektir.

## 6. Veri Kaynakları
İlk fazda Excel import korunacaktır.
İleride SQL / veri havuzu bağlantısına hazır yapı tasarlanacaktır.
LivaBudget özellikle referans alınacaktır.
SAP ve LivaBudget verileri ileride veri havuzu veya SQL üzerinden alınabilecek şekilde mimari hazırlanacaktır.

Excel ve SQL ayrı ayrı sistem gibi çalışmayacak.
İkisi de ortak veri modeline beslenecek.

Data Upload / Veri Yükleme ekranı Phase 2’de şu veri gruplarını destekleyecek şekilde planlanacaktır:
- WBS bütçe
- Gerçekleşen maliyet
- Hakediş icmali
- Kur tabloları (Planlanan USD kuru, Planlanan EUR kuru, Gerçekleşen USD kuru, Gerçekleşen EUR kuru)
- Enflasyon tabloları (TL enflasyonu, USD enflasyonu, EUR enflasyonu)
- İnşaat endeksi tabloları
- Emtia katsayı tabloları
- Avans / kesinti tabloları

Bu veriler ay/yıl bazında girilecek veya yüklenecektir.

## 7. Para Birimi Mantığı
Sistem planlanan ve gerçekleşen tutarları kesin TL varsaymayacaktır.

Veriler:
- TL
- USD
- EUR
- karışık para birimli

gelebilir.

Her satırda orijinal tutar, orijinal para birimi, dönem, uygulanan kur, TL karşılığı ve raporlama para birimi tutulacaktır.

Nakit akış ana raporu TL üretilecektir.
Ancak orijinal para birimi bilgisi korunacaktır.

## 8. Kur / Enflasyon / İnşaat Endeksi / Emtia Çarpanları
Her bütçe satırında aşağıdaki çarpanlar bağımsız seçilebilir olacaktır:

- Kur çarpanı
- Enflasyon çarpanı
- İnşaat endeksi çarpanı
- Emtia çarpanı

Katı kural uygulanmayacaktır.
TL kalem sadece enflasyonla çalışacak denmeyecek.
USD kalem sadece kurla çalışacak denmeyecek.
EUR kalem sadece kurla çalışacak denmeyecek.

Kullanıcı hangi çarpanı seçerse sistem o çarpanı uygulayacaktır.

Proje bazında aylık şu tablolar tanımlanabilecektir:
- Planlanan USD kuru
- Gerçekleşen USD kuru
- Planlanan EUR kuru
- Gerçekleşen EUR kuru
- TL enflasyonu
- USD enflasyonu
- EUR enflasyonu
- İnşaat endeksi
- Emtia katsayıları

## 9. Bütçe Hesaplama Mantığı
Sistem şu hesapları destekleyecektir:

- Baz bütçe
- Bugünün maliyeti
- Bugünün maliyeti + enflasyon
- Bugünün maliyeti + kur etkisi
- Bugünün maliyeti + inşaat endeksi
- Bugünün maliyeti + emtia çarpanı
- Seçili tüm çarpanlarla revize edilmiş planlanan maliyet

Formül mantığı:

Baz tutar = Metraj × Birim Fiyat

Güncel maliyet = Baz tutar × seçili çarpanlar

TL karşılığı = Güncel maliyet × ilgili ayın kur çarpanı

Kesinti sonrası tutar = TL karşılığı - seçili kesintiler

KDV dahil tutar = Kesinti sonrası tutar + KDV (KDV hesaplama sırası ve matrahı yasal mevzuata ve inşaat uygulama standartlarına göre araştırılıp önerilecek, rastgele yazılmayacaktır. Büyük değişiklik yapılmadan önce önerilen hesap mantığı raporlanacaktır.)

Nakit çıkış ayı = Maliyet ayı + ödeme vadesi

## 10. Bütçe Oluşturma Süreci
Kullanıcı “Yeni bütçe oluştur” dediğinde sistem sihirbaz mantığıyla ilerleyecektir.

Sistem şunları soracaktır:
- Bütçe tipi: Fizibilite / Keşif / Master / Revize
- Versiyon numarası
- Planlama başlangıç ayı
- Planlama bitiş ayı
- Dağıtım yöntemi
- Ödeme vadesi
- Çarpan seçimleri
- Kesinti ve KDV bilgileri

Versiyon mantığı:
Son Keşif 5 ise yeni Keşif 6 oluşturulacaktır.
Master hiç yoksa Master 1 oluşturulacaktır.
Çakışma varsa sistem uyarı verecektir.

## 11. Nakit Akış Mantığı
Maliyet planı ve nakit akış aynı şey değildir.

Maliyet, işin planlandığı veya gerçekleştiği ayda görünür.
Nakit çıkışı, ödeme vadesine göre ilgili aya kayar.

Nakit akışta şu kırılımlar ayrı görülecektir:
- Hakediş / imalat nakit çıkışı
- Avans ödemesi
- Avans mahsubu
- Kesintiler
- KDV
- Net nakit çıkışı
- Avans dahil nakit akış
- Avans hariç nakit akış

## 12. Hakediş İcmal Takibi
SAP’den gelen gerçekleşenlerde hakedişler çoğu zaman icmal detayında gelmez.
Sistem hakediş toplamını icmal kalemleriyle ilişkilendirebilmelidir.

Hakediş icmal modülünde şu bilgiler takip edilecektir:
- Hakediş toplamı
- İcmal kalemleri
- Metraj
- Birim fiyat
- Tutar
- PYP / Alt PYP / kaynak kodu eşleşmesi
- Kesinti
- KDV
- Avans mahsubu
- Dönem
- Yüklenici
- Sözleşme ilişkisi

## 13. Planlanan / Gerçekleşen / Kalan Bütçe
Sistem ay bazında planlanan, gerçekleşen ve kalan bütçeyi gösterecektir.

Geçmiş ay gerçekleşenleri geldikçe:
- Geçmiş dönemler kilitlenebilir.
- Geçmiş ay kilitlendiğinde PV hiçbir zaman AC’ye eşitlenmeyecek; planlanan değer planlanan olarak kalacak, gerçekleşen gerçekleşen olarak kalacak ve sapma korunacaktır.
- Kümülatif PV, BAC üzerinden hesaplanan planlanan ilerlemeyi gösterir.
- Kümülatif AC, gerçekleşen maliyeti gösterir.
- Maliyet gerçekleşme oranı kümülatif AC / BAC üzerinden kontrol edilecektir.
- PV, AC ve BAC birbirine karıştırılmayacaktır.
- EVM hesaplarında mevcut formüller bozulmadan, kullanılan oranların doğru isimlendirildiği kontrol edilecektir.
- Kalan bütçe yeniden hesaplanabilir.
- Önümüzdeki dönem yeniden planlanabilir.
- Revize nakit akış üretilebilir.

## 14. Dashboard ve Grafik Gereksinimleri
Mevcut dashboard bozulmayacaktır.

Ancak Phase 2’de grafikler, görseller ve kullanıcı deneyimi güçlendirilebilir.
Tasarım kararlarını model kendisi önerebilir.
Zayıf bulduğu grafikler için daha güçlü görselleştirme önerebilir.
Ancak mevcut hesap mantığı bozulmayacaktır.

Dashboard şu karşılaştırmaları gösterebilmelidir:
- Planlanan / gerçekleşen bütçe
- Planlanan / gerçekleşen maliyet
- Kalan bütçe
- Planlanan nakit akış / gerçekleşen nakit akış
- Revize nakit akış
- Planlanan kur / gerçekleşen kur
- Planlanan enflasyon / gerçekleşen enflasyon
- Planlanan inşaat endeksi / gerçekleşen inşaat endeksi
- Planlanan emtia / gerçekleşen emtia
- Avans dahil / avans hariç nakit akış

## 15. Benchmark Referansları
Aşağıdaki ürünler iş akışı ve veri modeli benchmarkı olarak incelenecektir:

- LivaBudget
- Procore Project Financials
- Oracle Primavera Unifier Cost / Contract Management
- Autodesk Construction Cloud Cost Management

Birebir ekran, kod, marka dili veya lisanslı şablon kopyalanmayacaktır.
Sadece iş akışı, veri modeli, modül ilişkileri ve kullanıcı deneyimi mantığı referans alınacaktır.

## 16. Geliştirme Kuralları
Kod yazmadan önce:
- Mevcut kaynak kodunu analiz et
- Mevcut WBS / PYP / Alt PYP / kaynak kodu yapısını anla
- Mevcut Excel import mantığını anla
- Mevcut dashboard ve EVM hesaplarını analiz et
- Bu dokümandaki hedeflerle mevcut sistemi karşılaştır
- Büyük mimari değişiklik yapmadan önce raporla

Ben onay vermeden büyük mimari değişiklik yapma.

## 17. İlk Analiz Tablosu
Kod yazmaya başlamadan önce şu tabloyu üret:

| Mevcut yapı | Korunacak alan | Geliştirilecek alan | Eksik olan | Risk | Phase 2 çözümü | İlk teknik adım |
|---|---|---|---|---|---|---|

## 18. Başarı Kriteri
Kullanıcı sistemde:
- Yeni bütçe oluşturabilmeli
- Keşif / Master / Revize bütçe seçebilmeli
- Versiyon takibi yapabilmeli
- Excel’den veri yükleyebilmeli
- İleride SQL/veri havuzundan veri çekebilmeli
- Para birimi fark etmeksizin planlanan ve gerçekleşeni takip edebilmeli
- Kur, enflasyon, inşaat endeksi ve emtia çarpanlarını seçerek bütçe oluşturabilmeli
- TL nakit akış üretebilmeli
- Avans nakit akışını ayrı görebilmeli
- Hakediş icmallerini takip edebilmeli
- Mevcut EVM dashboard çıktısını bozmadan daha güçlü raporlar alabilmelidir.
