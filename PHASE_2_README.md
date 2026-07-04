# PHASE_2_README

Bu dizin, Folkart Kurumsal İnşaat Proje Platformu projesinin **Phase 2 (2. Aşama - Bütçe ve Maliyet Yönetim Platformu)** geliştirmeleri için oluşturulmuş çalışma alanıdır. 

Phase 1 / MVP versiyonu diğer dizinde dondurulmuş olup, tüm yeni özellik ve genişletmeler bu kopyalanan proje üzerinde yürütülecektir.

---

## 1. Phase 2 Projesinin Amacı ve Phase 1'den Farkı

*   **Phase 1 (MVP)**: Statik kazanılmış değer hesapları, temel Excel import özelliği ve basit hakediş/nakit akış listelerini barındıran sunulabilir bir MVP aşamasıdır.
*   **Phase 2 (Bütçe & Maliyet Yönetim Platformu)**: Mevcut EVM ve dashboard sistemini bozmadan, yaşayan bir bütçe oluşturma sihirbazı, bütçe versiyon/revizyon yönetimi, çoklu para birimi/kur çarpanları, hakediş icmal takibi, avans ve kesintileri içeren dinamik bir nakit akışı platformuna dönüştürmeyi amaçlar.

---

## 2. Phase 2 Geliştirmelerinde Okunması Gereken Belgeler

Geliştirmeye başlamadan önce sırasıyla aşağıdaki dokümanlar okunmalı ve kurallarına uyulmalıdır:

1.  [`AGENTS.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/AGENTS.md): Ortak çalışma kuralları, tasarım/tema sınırlamaları ve başarı kriterleri.
2.  [`PRODUCT_VISION_PHASE_2.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/PRODUCT_VISION_PHASE_2.md): Ürün vizyonu, kapsam, hesaplama formülleri ve kabul kriterleri.
3.  [`SYSTEM_ARCHITECTURE.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/SYSTEM_ARCHITECTURE.md): Klasör yapısı, veri akışı ve Excel import mekanizması analizi.
4.  [`DATA_MODEL_ANALYSIS.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/DATA_MODEL_ANALYSIS.md): TypeScript tipleri, WBS kod yapısı ve eklenmesi gereken veri modelleri.
5.  [`COMPONENT_MAP.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/COMPONENT_MAP.md): Ekranların ve ortak bileşenlerin hiyerarşik ağaç yapısı ve kırılgan alanlar.
6.  [`UI_INVENTORY.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/UI_INVENTORY.md): Mevcut 13 ekranın metrik/grafik envanteri ve yeni ekran taslakları.
7.  [`KNOWN_LIMITATIONS.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/KNOWN_LIMITATIONS.md): Mevcut sistemin yapamadığı iş süreçleri, SQL entegrasyon hazırlık seviyesi ve regresyon riskleri.
8.  [`BENCHMARK.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/BENCHMARK.md): LivaBudget, Procore, Unifier ve Autodesk Cost Management benchmark analizleri.
9.  [`IDEAS_BACKLOG.md`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/IDEAS_BACKLOG.md): Henüz kesinleşmemiş fikirler, açık sorular ve karar bekleyen başlıklar.

---

## 3. Kod Yazmadan Önce İzlenecek Sıra

1.  Yukarıdaki belgelerin tamamını okuyun ve sistemin mevcut çalışmasını anlayın.
2.  Mevcut WBS kodlama yapısını (`RESOURCE_LIST`) ve EVM motorunu (`evmEngine.ts`) inceleyin.
3.  Yapmak istediğiniz geliştirmeyi içeren detaylı bir uygulama planı (Implementation Plan) hazırlayın.
4.  **BÜYÜK MİMARİ DEĞİŞİKLİK YAPMADAN ÖNCE KULLANICIDAN YAZILI ONAY ALIN.**
5.  Uygulamayı yaparken, mevcut hesaplama doğruluğunu korumak için her aşamada `npm run build` ile build kontrolü sağlayın.

---

## 4. Temel Kurallar ve Regresyon Koruması

*   **Dashboard ve EVM'i Koru**: Mevcut çalışan Dashboard, EVM hesaplama motoru ve WBS tablosunun veri bütünlüğü asla bozulmamalıdır.
*   **Ek Modül Yaklaşımı**: Yeni bütçe versiyonlama, kur tabloları ve hakediş icmali özellikleri mevcut yapıları kırarak değil, ek veri modelleri ve yeni ekran bileşenleri (`screens/` altında yeni TSX dosyaları) olarak eklenmelidir.
*   **Açık Tema**: Koyu tema kesinlikle kullanılmamalı, yeni ekranlarda da `theme.css` içindeki açık renk Folkart teması esas alınmalıdır.
