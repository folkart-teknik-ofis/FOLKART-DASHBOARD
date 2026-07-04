# UI_INVENTORY

## 1. Mevcut Ekranlar Envanteri

Uygulama içerisinde kullanıcıya sunulan 13 adet ekranın ayrıntılı dökümü aşağıdadır:

### A. Kontrol Paneli (`dashboard`)
*   **Amaç**: Portföy düzeyinde genel durum, finansal özet ve EVM performans takibi.
*   **KPI Metrikleri**: BAC, PV, AC, EV, EAC, CV, SV, CPI, SPI, VAC.
*   **Grafikler**: Bütçe Karşılaştırma Grafiği (Recharts BarChart).
*   **Tablolar**: Proje Performans Özeti (Proje Adı, Durum, Fiziki %, EVM Değerleri).
*   **Filtreler**: Üst barda çoklu Proje Seçimi ve Rapor Dönemi seçimi.
*   **Kullanıcı Akışı**: Proje durumlarını gözlemleme, Excel raporu dışa aktarma, bildirimleri inceleme.

### B. Portföy (`portfolio`)
*   **Amaç**: Projelerin detaylı künye bilgilerini kartlar halinde listeleme.
*   **KPI Metrikleri**: Her proje kartında Fiziki İlerleme %, Süre (Ay) İlerleme %, BAC, Harcanan Tutar %, m² maliyeti.
*   **Grafikler**: Yok (Kart içi progress barlar mevcut).
*   **Tablolar**: Yok (Kart görünümü).
*   **Filtreler**: Proje Kodu/Adı ile arama çubuğu, Durum filtresi (Aktif, Beklemede, Tamamlandı, Planlama).
*   **Kullanıcı Akışı**: Proje kartlarını inceleme, proje durumlarını değiştirme, proje yöneticisi ve tarih bilgilerini görme.

### C. Bütçe (`budget`)
*   **Amaç**: WBS (İş Kırılım Yapısı) bazında bütçe kırılımlarını ve aylık plan/gerçekleşen maliyet dağılımlarını inceleme.
*   **KPI Metrikleri**: Toplam BAC, Toplam AC, Toplam EV, Toplam EAC.
*   **Grafikler**: Yok.
*   **Tablolar**: Hiyerarşik WBS Ağaç Tablosu (Ana PYP -> Alt PYP -> Kaynak Kodu basamaklı tablo; her satır için aylık miktar, birim fiyat ve tutar detayları görüntülenebilir).
*   **Filtreler**: Proje filtreleme dropdown menüsü.
*   **Kullanıcı Akışı**: WBS kırılımlarını açıp kapatma, aylık planlanan ve gerçekleşen birim fiyat/miktar detaylarını açılır panelde inceleme.

### D. EVM Analizi (`evm`)
*   **Amaç**: Kazanılmış Değer Analizi (EVM) performans indekslerinin trend takibi.
*   **KPI Metrikleri**: CPI (Maliyet Performans İndeksi), SPI (Program Performans İndeksi).
*   **Grafikler**: Performans İndeksleri Trendi (Recharts AreaChart - CPI ve SPI zaman serisi).
*   **Tablolar**: EVM Performans Tablosu (Kritik sınırı aşan sapmaların listesi).
*   **Filtreler**: Üst bar Proje ve Rapor Dönemi.
*   **Kullanıcı Akışı**: Zaman serisi grafik üzerinde CPI/SPI dalgalanmalarını izleme, sapmaları kontrol etme.

### E. Varyans (`variance`)
*   **Amaç**: Planlanan bütçe ile gerçekleşen maliyetler arasındaki sapmaları (Varyans) inceleme.
*   **KPI Metrikleri**: CV (Maliyet Sapması), SV (Program Sapması).
*   **Grafikler**: Varyans Karşılaştırma Grafiği (Recharts BarChart - CV ve SV barları).
*   **Tablolar**: Sapma Analizi Detay Tablosu (WBS düzeyinde sapma miktarı ve sapma yüzdesi).
*   **Filtreler**: Proje ve Rapor Dönemi.
*   **Kullanıcı Akışı**: En çok bütçe aşımı veya gecikme yaşayan WBS kalemlerini tespit etme.

### F. Hakediş (`hakedis`)
*   **Amaç**: Yüklenici hakedişlerinin tutar, dönem ve onay durumlarının izlenmesi.
*   **KPI Metrikleri**: Toplam Hakediş Tutarı, Onaylanan Tutar, Onay Bekleyen Adet, İtirazlı Adet.
*   **Grafikler**: Yok.
*   **Tablolar**: Hakediş Listesi Tablosu (Hakediş No, Tanım, Dönem, Tutar, Durum, Tarih, Onaylayan).
*   **Filtreler**: Üst bar Proje seçimi.
*   **Kullanıcı Akışı**: Yeni hakediş kaydı ekleme, listeyi Excel'e ihraç etme.

### G. S-Eğrisi (`scurve`)
*   **Amaç**: Planlanan kümülatif ilerleme ile gerçekleşen kümülatif ilerlemenin karşılaştırılması.
*   **KPI Metrikleri**: Planlanan İlerleme %, Gerçekleşen İlerleme %, Kazanılmış İlerleme %.
*   **Grafikler**: S-Curve İlerleme Grafiği (Recharts ComposedChart - Planlanan kümülatif alan, Gerçekleşen çizgi).
*   **Tablolar**: Yok.
*   **Filtreler**: Proje seçimi.
*   **Kullanıcı Akışı**: Proje takvim performansını görsel olarak S-Eğrisi üzerinden izleme.

### H. Nakit Akışı (`cashflow`)
*   **Amaç**: Proje gelir ve giderlerinin aylık bazda planlanan ve gerçekleşen nakit hareketlerinin izlenmesi.
*   **KPI Metrikleri**: Toplam Planlanan Nakit Çıkışı, Toplam Gerçekleşen Nakit Çıkışı.
*   **Grafikler**: Nakit Akış Grafiği (Recharts BarChart - Aylık planlanan ve gerçekleşen nakit çıkışı).
*   **Tablolar**: Aylık Nakit Akış Detay Tablosu.
*   **Filtreler**: Proje seçimi.
*   **Kullanıcı Akışı**: Aylık ödeme planını ve gerçekleşen ödemeleri izleme.

### I. Raporlar (`report`)
*   **Amaç**: Proje durum raporunun yazdırılabilir (print-friendly) formatta üretilmesi.
*   **KPI Metrikleri**: Proje Künye Bilgileri, EVM Özet Değerleri.
*   **Grafikler**: Yok.
*   **Tablolar**: Rapor Özet Tablosu.
*   **Filtreler**: Proje seçimi.
*   **Kullanıcı Akışı**: Raporu PDF/Print formatında yazdırma.

### J. Veri Yükleme (`dataupload`)
*   **Amaç**: Excel formatındaki bütçe ve hakediş verilerini sisteme aktarma.
*   **KPI Metrikleri**: Yüklenen Satır Sayısı, Hata Sayısı.
*   **Grafikler**: Yok.
*   **Tablolar**: Excel Önizleme Tablosu, Dosya Yükleme Geçmişi Tablosu.
*   **Filtreler**: Dönem (Ay/Yıl) seçimi.
*   **Kullanıcı Akışı**: Şablon indirme, dosya yükleme, doğrulama adımlarını geçip veriyi kaydetme.

---

## 2. Eksik veya Geliştirilmesi Gereken Kullanıcı Akışları

1.  **Dinamik Grafik Bağlantıları**: S-Curve ve Cashflow grafiklerinin statik mock verilerden kurtarılıp Excel'den yüklenen veya state'te bulunan `projectWbsValues` verilerinden dinamik beslenmesi sağlanmalıdır.
2.  **Hakediş İcmal İlişkilendirmesi**: Hakediş tablosundaki bir satıra tıklandığında, hakedişin hangi iş kalemlerinde yapıldığını gösteren detay (icmal) görünüm akışı eksiktir.
3.  **Proje Ekleme Sihirbazı**: Proje ekleme modalı mevcuttur ancak yeni bütçe yapısı eklendiğinde bütçe tipinin ve varsayılan çarpanların seçileceği adımlı bir sihirbaza dönüştürülmelidir.
4.  **Bütçe Revizyon Akışı**: Mevcut bütçenin kilitlenip yeni bir revizyon versiyonu (örn: R1, R2) oluşturulması akışı eksiktir.

---

## 3. Görsel Arayüzü Güçlendirme Önerileri

*   **Zayıf Grafikler**: Dashboard üzerindeki bütçe karşılaştırma grafiği tek renkli dikey barlardan oluşmaktadır. Buraya kazanılmış değer metoduna uygun, BAC çizgili Composed grafikler eklenebilir.
*   **EVM Trend Alanı**: EVM performans trend grafiğindeki çizgilerin altına hafif kurumsal degrade (gradient fill) eklenerek görsellik premium seviyeye taşınabilir.
*   **WBS Ağaç Vurguları**: WBS tablosunda düzeyler arasındaki görsel hiyerarşi (girintiler, kalınlıklar, renklendirmeler) daha belirgin hale getirilebilir.

---

## 4. Phase 2'de Eklenecek Ekran Taslakları

Mevcut ekranların yapısı bozulmadan, Phase 2 kapsamında aşağıdaki yeni yönetim modülleri / ekranları sisteme entegre edilebilir:

1.  **Bütçe Sihirbazı Ekranı**: Yeni bütçe versiyonlarının (Fizibilite, Keşif, Master, Revize) oluşturulduğu, planlama tarihlerinin ve ödeme vadelerinin girildiği adımlı sihirbaz arayüzü.
2.  **Hakediş İcmal Giriş Ekranı**: Hakediş satırına bağlı olarak metraj, birim fiyat, kesinti, KDV ve avans mahsubunun WBS düzeyinde girilebileceği veya Excel'den aktarılabileceği detay ekranı.
3.  **Varsayım ve Çarpan Yönetim Paneli**: Kur, enflasyon, inşaat endeksi ve emtia çarpanlarının proje bazında aylık tablolar halinde tanımlanıp güncellenebileceği matris ekranı.
4.  **Gelişmiş Nakit Akış Ekranı**: Avans ödemelerinin, avans mahsuplarının ve kesintilerin (KDV dahil/hariç) net nakit çıkışlarını nasıl etkilediğini gösteren çok kırılımlı nakit akış tablosu ve grafiği.
