# BUSINESS_WORKFLOWS

Bu belgede Folkart Bütçe ve Maliyet Yönetim Platformu Phase 2 kapsamında yer alan iş süreçlerinin (workflows) iş mantığı, girdileri, kullanıcı kararları, hesaplamaları, çıktıları ve ilişkili modülleri adım adım tanımlanmıştır.

---

## 1. Bütçe Oluşturma Süreci

*   **Açıklama**: Proje için yeni bir bütçe sürümü oluşturma sihirbazıdır.
*   **Başlangıç**: Kullanıcının "Yeni Bütçe Oluştur" sihirbazını başlatması.
*   **Girdiler**:
    *   Proje Tanımı (Proje Kodu, Proje Adı, Bölgesi vb.)
    *   Bütçe Tipi seçimi (Fizibilite / Keşif / Master / Revize)
    *   Bütçe Versiyon No / Kodu (örn: M1, K3, R1)
    *   Planlama Başlangıç Dönemi (Ay/Yıl) ve Bitiş Dönemi (Ay/Yıl)
    *   Baz Para Birimi (TRY, USD, EUR)
*   **Kullanıcı Kararları**:
    *   Varsayılan bütçe dağıtım yöntemi (Lineer, Çan Eğrisi, Ön Ağırlıklı vb.)
    *   Genel ödeme vadesi (örn: "Maliyet ayından 30 gün/1 ay sonra ödeme")
    *   Proje düzeyinde uygulanacak varsayılan KDV oranı ve kesinti parametreleri.
*   **Hesaplamalar**:
    *   `Bütçe Süresi (Ay) = Bitiş Ayı - Başlangıç Ayı + 1`
*   **Çıktılar**:
    *   Taslak durumunda yeni bir `BudgetVersion` kaydı.
    *   Bütçeye ait planlama takvim parametreleri.
*   **Kullanılan Modüller**: Bütçe Sihirbazı Modülü, Versiyon Yönetim Modülü.

---

## 2. Keşif Bütçesi Oluşturma Süreci

*   **Açıklama**: Fizibilite aşaması sonrasında, inşaat öncesi detaylı iş kalemleri bazında bütçenin oluşturulmasıdır.
*   **Başlangıç**: Taslak veya yeni bütçenin "Keşif" olarak seçilmesi ve WBS yapısının kurulması.
*   **Girdiler**:
    *   3 Seviyeli WBS Ağacı (Ana PYP -> Alt PYP -> Kaynak Kodları)
    *   İş kalemlerine ait metrajlar (Miktar)
    *   Birim Fiyatlar
    *   Satır bazında para birimleri (TRY, USD, EUR)
*   **Kullanıcı Kararları**:
    *   Bütçe giriş seviyesi tercihi (Kaynak kodu bazlı mı, Alt PYP bazlı mı yoksa Ana PYP bazlı mı girileceği)
    *   Metrajların Excel import ile mi yoksa manuel mi girileceği
*   **Hesaplamalar**:
    *   `Bütçe Satırı Baz Tutarı = Metraj * Birim Fiyat`
    *   `Ana/Alt PYP Toplamı = Altındaki kalemlerin Baz Tutarları Toplamı`
*   **Çıktılar**:
    *   Detaylı Keşif Bütçesi Listesi (WBS bazlı tablolarda gösterilir).
*   **Kullanılan Modüller**: WBS Editör Modülü, Bütçe Modülü.

---

## 3. Master Bütçe Oluşturma Süreci

*   **Açıklama**: İhale süreçleri tamamlandıktan sonra, projenin resmi hedef/kontrat bütçesinin kilitlenmesidir.
*   **Başlangıç**: Onaylanan Keşif bütçesinin "Master Bütçe" olarak tescil edilmesi.
*   **Girdiler**:
    *   Nihai onaylı Keşif bütçesi sürümü (örn: Keşif v3)
    *   Resmi yüklenici sözleşme tutarları
*   **Kullanıcı Kararları**:
    *   Hangi Keşif sürümünün baz alınacağı
    *   Master Bütçenin kilitlenme ve resmi onay kararı
*   **Hesaplamalar**:
    *   Keşif bütçesi kalemlerinin Master Bütçe (v1) olarak kopyalanması.
    *   EVM hesaplamalarında kullanılacak baz bütçe değerinin (`BAC`) sabitlenmesi.
*   **Çıktılar**:
    *   Kilitlenmiş, değiştirilemez `Master Budget Version 1` kaydı.
    *   Projenin resmi `BAC` (Budget at Completion) değeri.
*   **Kullanılan Modüller**: Versiyon Yönetim Modülü, Onay/Kilit Modülü.

---

## 4. Revize Bütçe Oluşturma Süreci

*   **Açıklama**: Proje devam ederken oluşan kapsam değişiklikleri, aşırı sapmalar veya ekonomik dalgalanmalar nedeniyle bütçenin güncellenmesidir.
*   **Başlangıç**: Kullanıcının aktif bütçeyi revizyona açması.
*   **Girdiler**:
    *   Aktif Master veya en son onaylı Revize Bütçe verileri
    *   Raporlama tarihine kadar gerçekleşmiş fiili maliyetler (`AC`)
    *   Gelecek dönemlere ait yeni metraj/fiyat öngörüleri
*   **Kullanıcı Kararları**:
    *   Revizyon kapsamı (tüm kalemler mi yoksa sadece belirli PYP'ler mi?)
    *   Geçmiş aylardaki gerçekleşenlerin aynen korunup dondurulması kararı.
*   **Hesaplamalar**:
    *   `Geçmiş Dönem Planlanan = Gerçekleşen Maliyet (AC)`
    *   `Gelecek Dönem Planlanan = Yeni Revize Metraj * Yeni Revize Birim Fiyat`
    *   Yeni `EAC` (Tahmini Nihai Maliyet) değerinin hesaplanarak revize bütçenin `BAC` değeri yapılması.
*   **Çıktılar**:
    *   Yeni bir Revize Bütçe Versiyonu (örn: Revize R1).
*   **Kullanılan Modüller**: Versiyon Yönetim Modülü, WBS Editör Modülü.

---

## 5. Hakediş İcmal Süreci

*   **Açıklama**: Taşeronlardan gelen aylık imalat hakedişlerinin WBS düzeyinde detaylandırılarak onaylanması ve ödemeye bağlanması.
*   **Başlangıç**: Taşeronun aylık hakediş icmal formunu iletmesi veya sisteme girilmesi.
*   **Girdiler**:
    *   Dönem içi yapılan imalat metrajları
    *   Sözleşme birim fiyatları
    *   Varsa avans mahsup oranı, teminat kesinti oranları ve KDV oranı
*   **Kullanıcı Kararları**:
    *   İcmal satırlarının bütçedeki hangi WBS (Kaynak Kodu/Alt PYP) ile eşleşeceği
    *   Hakediş durumunun belirlenmesi (Onay Bekliyor / Onaylandı / İtirazlı)
*   **Hesaplamalar**:
    *   `Brüt Hakediş Tutarı = İmalat Metrajı * Birim Fiyat`
    *   `Avans Mahsubu = Brüt Hakediş Tutarı * Avans Mahsup Oranı`
    *   `Teminat/Vergi Kesintisi = Brüt Hakediş Tutarı * Kesinti Oranı`
    *   `Kesinti Sonrası Tutar = Brüt Hakediş Tutarı - Avans Mahsubu - Kesintiler`
    *   `KDV Tutarı = Kesinti Sonrası Tutar * KDV Oranı`
    *   `Net Hakediş Tutarı (Ödeme Tutarı) = Kesinti Sonrası Tutar + KDV Tutarı`
*   **Çıktılar**:
    *   WBS kırılımlı onaylı hakediş icmali tablosu.
    *   Nakit akışına beslenecek hakediş bazlı fiili nakit çıkışı verisi.
*   **Kullanılan Modüller**: Hakediş İcmal Modülü, Sözleşme Yönetim Modülü.

---

## 6. Gerçekleşen Maliyet Takibi

*   **Açıklama**: Ay sonunda projede gerçekleşen tüm fiili giderlerin izlenmesi ve bütçe kalemlerine yansıtılmasıdır.
*   **Başlangıç**: Muhasebe döneminin kapanması veya SAP/Excel gerçekleşen verilerinin yüklenmesi.
*   **Girdiler**:
    *   Muhasebe fiili harcama dökümleri (malzeme faturaları, genel giderler vb.)
    *   Onaylanan aylık yüklenici hakediş tutarları
    *   Rapor Dönemi (Ay/Yıl)
*   **Kullanıcı Kararları**:
    *   SAP'den gelen genel maliyet kalemlerinin hangi WBS/Kaynak koduna dağıtılacağı (Paylaştırma anahtarı veya doğrudan eşleştirme).
*   **Hesaplamalar**:
    *   `WBS Satırı AC = Eşleşen Fatura Tutarları + Onaylı Brüt Hakediş Tutarları`
    *   `Küm. AC = Seçilen aya kadar olan aylık AC'lerin toplamı`
*   **Çıktılar**:
    *   WBS bazında güncel kümülatif `AC` (Actual Cost) verileri.
*   **Kullanılan Modüller**: Gerçekleşen Maliyet Modülü, Entegrasyon Modülü.

---

## 7. Planlanan / Gerçekleşen Karşılaştırması

*   **Açıklama**: Projenin zaman ve maliyet hedeflerinden sapmalarının izlendiği analiz sürecidir.
*   **Başlangıç**: Karşılaştırma veya Varyans ekranlarının açılması.
*   **Girdiler**:
    *   Seçilen rapor ayındaki kümülatif `PV` (Planlanan), `AC` (Gerçekleşen) ve `EV` (Fiziki İlerleme * BAC).
*   **Kullanıcı Kararları**:
    *   Karşılaştırmanın hangi bütçe versiyonuna (Master mı, en son Revize mi) göre yapılacağı seçimi.
*   **Hesaplamalar**:
    *   `CV (Maliyet Sapması) = EV - AC`
    *   `SV (Takvim Sapması) = EV - PV`
    *   `CPI = EV / AC` ve `SPI = EV / PV`
*   **Çıktılar**:
    *   S-Eğrisi grafiği üzerinde sapma gösterimi.
    *   Varyans analiz tablosu (kırmızı/yeşil uyarılar).
*   **Kullanılan Modüller**: Varyans Analiz Modülü, EVM Hesap Motoru.

---

## 8. Kalan Bütçe Hesabı (Forecast)

*   **Açıklama**: Projenin tamamlanması için gereken kalan bütçenin ve nihai bütçe aşım riskinin hesaplanmasıdır.
*   **Başlangıç**: Raporlama dönemi kapatıldığında veya revizyon öncesinde kalan bütçe analizinin yapılması.
*   **Girdiler**:
    *   `BAC` (Onaylı Bütçe), `AC` (Gerçekleşen), `EV` (Kazanılmış Değer), `CPI` (Maliyet Performansı).
*   **Kullanıcı Kararları**:
    *   Kalan bütçe hesaplama senaryosunun seçilmesi:
        *   *Senaryo A*: Kalan işlerin planlandığı gibi gideceği varsayımı (`ETC = BAC - EV`).
        *   *Senaryo B*: Kalan işlerin mevcut CPI trendinde devam edeceği varsayımı (`ETC = (BAC - EV) / CPI`).
*   **Hesaplamalar**:
    *   Seçilen senaryoya göre `ETC` (Kalan Bütçe) hesabı.
    *   `EAC (Tahmini Nihai Maliyet) = AC + ETC`.
    *   `VAC (Tamamlama Sapması) = BAC - EAC`.
*   **Çıktılar**:
    *   Kalan Bütçe Tahmini (ETC) ve Nihai Bütçe Aşım Riski (VAC).
*   **Kullanılan Modüller**: EVM Hesap Motoru, Bütçe Modülü.

---

## 9. Çarpan Yönetimi

*   **Açıklama**: Bütçeyi güncelleyecek dış ekonomik etkenlerin (kur, enflasyon, endeks vb.) aylık bazda tanımlanmasıdır.
*   **Başlangıç**: Katsayı yönetim ekranının açılması.
*   **Girdiler**:
    *   Aylık USD/EUR kuru tahminleri ve gerçekleşenleri
    *   Aylık planlanan ve gerçekleşen yurt içi/dışı enflasyon oranları (%)
    *   İnşaat maliyet endeks serileri
    *   Emtia (Demir, çimento vb.) fiyat katsayıları
*   **Kullanıcı Kararları**:
    *   Tahminlerin hangi veri kaynağından (TCMB, TUİK, özel banka tahminleri) alınacağı.
    *   Gelecek aylara ait oranların manuel güncellenmesi.
*   **Hesaplamalar**:
    *   Aylık oranların kümülatif çarpanlara dönüştürülmesi:
        *   `Küm. Çarpan (Ay N) = Küm. Çarpan (Ay N-1) * (1 + Aylık Oran / 100)`
*   **Çıktılar**:
    *   Projeye özel Aylık Çarpan ve Katsayı Matrisi.
*   **Kullanılan Modüller**: Katsayı Yönetim Modülü.

---

## 10. Varsayım Yönetimi

*   **Açıklama**: Bütçe satırlarına ekonomik varsayımların/çarpanların atandığı ve güncellendiği süreçtir.
*   **Başlangıç**: WBS bütçe ekranında satır düzeyinde çarpan seçimlerinin yapılması.
*   **Girdiler**:
    *   Bütçe Satırı Baz Tutarı (Metraj * Birim Fiyat)
    *   Aylık Çarpan Matrisi
*   **Kullanıcı Kararları**:
    *   Bütçe satırına hangi çarpanların (Kur, Enflasyon, İnşaat Endeksi, Emtia) uygulanacağı seçimi (Örn: "Demir bütçesi emtia ve kur çarpanıyla çalışsın").
*   **Hesaplamalar**:
    *   `Güncel Maliyet = Baz Tutar * Uygulanan Çarpanlar`
*   **Çıktılar**:
    *   Gelecek aylara ait revize edilmiş (çarpanlı) Planlanan Maliyet serisi.
*   **Kullanılan Modüller**: Varsayım Motoru, Bütçe Hesaplama Modülü.

---

## 11. Nakit Akışı Oluşturma Süreci

*   **Açıklama**: Maliyet bütçesinden hareketle, fiili para çıkış tarihlerini ve tutarlarını gösteren nakit akış tablosunun üretilmesidir.
*   **Başlangıç**: Nakit Akışı ekranının güncellenmesi veya çalıştırılması.
*   **Girdiler**:
    *   WBS Planlanan Maliyet Takvimi (aylık dağılım)
    *   Bütçe satırlarına atanan Ödeme Vadeleri (Vade kaydırma)
    *   KDV Oranları ve Kesinti Oranları
*   **Kullanıcı Kararları**:
    *   Nakit akışının hangi bütçe versiyonundan (Master / Revize) üretileceği.
*   **Hesaplamalar**:
    *   `Nakit Çıkış Ayı = Maliyet Ayı + Ödeme Vadesi (Ay)`
    *   `Satır Brüt Nakit Çıkışı = Maliyet Tutarı * (1 - Kesinti Oranı) * (1 + KDV Oranı)`
*   **Çıktılar**:
    *   Aylara göre dağıtılmış Nakit Akış Tablosu ve Grafiği.
*   **Kullanılan Modüller**: Nakit Akış Modülü.

---

## 12. Avans Nakit Akışı Süreci

*   **Açıklama**: Proje başlangıcında taşeronlara yapılacak avans ödemelerinin ve bu avansların hakedişlerden mahsup edilmesinin nakit akışına etkisidir.
*   **Başlangıç**: Bütçede veya sözleşmede avans parametrelerinin girilmesi.
*   **Girdiler**:
    *   Avans Tutarı veya Avans Yüzdesi (örn: Bütçenin %10'u)
    *   Avans Ödeme Ayı
    *   Avans Mahsup Oranı (Her hakedişten yapılacak kesinti oranı, örn: %15)
*   **Kullanıcı Kararları**:
    *   Avansın hangi ayda ödeneceği ve mahsup sürecinin ne zaman başlayacağı.
*   **Hesaplamalar**:
    *   `Avans Ödeme Ayı Nakit Çıkışı = Avans Tutarı` (Nakit çıkışına doğrudan yansır)
    *   Her hakediş ayında:
        *   `Avans Mahsup Tutarı = Brüt Hakediş * Mahsup Oranı`
        *   `Hakediş Ödeme Tutarı = Brüt Hakediş - Avans Mahsup Tutarı - Diğer Kesintiler`
*   **Çıktılar**:
    *   "Avans Dahil" ve "Avans Hariç" seçenekli nakit akış raporu.
    *   Avans Kalan Bakiyesi takibi.
*   **Kullanılan Modüller**: Avans Yönetim Modülü, Nakit Akış Modülü.

---

## 13. Bütçe Revizyonu Süreci

*   **Açıklama**: Projede resmi bir revizyon bütçesi sürümü oluşturma ve kilitlenme akışıdır.
*   **Başlangıç**: Yönetim kararıyla resmi revizyon (R1, R2 vb.) tetiklenmesi.
*   **Girdiler**:
    *   Mevcut onaylı bütçe sürümü
    *   Gerekçe raporu (kapsam değişikliği, fiyat artışı vb.)
*   **Kullanıcı Kararları**:
    *   Eski bütçenin arşivlenmesi ve yeni bütçenin "Aktif Versiyon" yapılması onayı.
*   **Hesaplamalar**:
    *   Eski bütçe satırlarının kopyalanarak dondurulması.
    *   Yeni bütçenin `activeBudgetVersionId` olarak atanması.
*   **Çıktılar**:
    *   Sistem genelinde yeni bütçe versiyonunun aktif hale getirilmesi.
*   **Kullanılan Modüller**: Versiyon Yönetim Modülü.

---

## 14. Dashboard Güncelleme Süreci

*   **Açıklama**: Herhangi bir veri yükleme veya filtre değişimi sonrası kontrol panelindeki tüm metriklerin güncellenmesidir.
*   **Başlangıç**: Kullanıcının rapor ayını değiştirmesi, yeni bir bütçe yüklemesi veya hakediş onaylaması.
*   **Girdiler**:
    *   Güncel `projects` state'i
    *   Güncel `projectWbsValues` state'i
    *   Seçili Rapor Dönemi ve Projeler
*   **Kullanıcı Kararları**:
    *   Görselleştirilecek verinin konsolide mi yoksa tek proje bazlı mı olacağı seçimi.
*   **Hesaplamalar**:
    *   Seçili dönem katsayısına göre WBS değerlerinin ölçeklenmesi.
    *   Scaled values üzerinden portföy toplamlarının (`BAC`, `PV`, `AC`, `EV`) alınması.
    *   `calculateEVM` fonksiyonunun çalıştırılarak tüm CPI/SPI/EAC metriklerinin güncellenmesi.
*   **Çıktılar**:
    *   Yenilenmiş Dashboard ekranı grafik ve metrik kartları.
*   **Kullanılan Modüller**: Dashboard Modülü, EVM Hesap Motoru.
