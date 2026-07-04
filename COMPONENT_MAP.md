# COMPONENT_MAP

## 1. Çekirdek Component Listesi ve Konumları

Uygulamadaki tüm ana bileşenlerin (Screens) ve alt ortak bileşenlerin (Shared Components) dosyaları ve hiyerarşik ilişkileri aşağıdaki tabloda listelenmiştir:

| Bileşen Adı | Dosya Yolu | Açıklama |
|---|---|---|
| `App` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Ana düzen (Shell), Sidebar menüsü, üst seçim barları ve ekran dağıtıcısı. |
| `DashboardScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Portföy bazlı bütçe ve EVM performans özet ekranı. |
| `PortfolioScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Projelerin künye kartlarını ve genel durumlarını listeleyen ekran. |
| `BudgetScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | WBS bazlı hiyerarşik bütçe, aylık planlama ve gerçekleşen metraj/bf tablosu. |
| `EVMScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Kazanılmış değer metriklerinin (CPI, SPI) grafiksel ve performans tablosu analizi. |
| `VarianceScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Bütçe ve Maliyet sapmalarını gösteren grafik ve varyans listesi. |
| `HakedisScreen` | [`src/app/screens/HakedisScreen.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/screens/HakedisScreen.tsx) | Hakediş kayıtları, onay durumları ve onay süreci özet ekranı. |
| `SCurveScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Planlanan ve gerçekleşen kümülatif ilerleme (S-Eğrisi) grafiği. |
| `CashFlowScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Aylık planlanan ve gerçekleşen nakit akış çubuk grafiği ve tablosu. |
| `ReportScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Proje durum raporları çıktısı ve yazdırılabilir sürüm görünümü. |
| `DataUploadScreen` | [`src/app/App.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/App.tsx) | Excel bütçe ve hakediş şablonları indirme/yükleme ve veri işleme sihirbazı. |
| `ResourceListScreen` | [`src/app/screens/ResourceListScreen.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/screens/ResourceListScreen.tsx) | WBS/PYP kırılımlı kaynak kataloğu ve Excel şablon yönetimi. |
| `UsersScreen` | [`src/app/screens/UsersScreen.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/screens/UsersScreen.tsx) | Kullanıcı yetki ve durum tablosu. |
| `GlossaryScreen` | [`src/app/screens/GlossaryScreen.tsx`](file:///C:/Users/sefika.huseyinbeyli/OneDrive%20-%20Saya%20Holding/Desktop/F%C4%B0GMA%20TASARIM%20GEL%C4%B0%C5%9ET%C4%B0RME%20-%20PHASE%202/src/app/screens/GlossaryScreen.tsx) | Terimler rehberi, EVM formül kartları ve gösterge endeks kılavuzu. |

---

## 2. Hiyerarşik Bileşen Ağacı (Component Tree)

```mermaid
graph TD
    App[App Shell]
    App --> DashboardScreen
    App --> PortfolioScreen
    App --> BudgetScreen
    App --> EVMScreen
    App --> VarianceScreen
    App --> HakedisScreen
    App --> SCurveScreen
    App --> CashFlowScreen
    App --> ReportScreen
    App --> DataUploadScreen
    App --> ResourceListScreen
    App --> UsersScreen
    App --> GlossaryScreen

    subgraph Ortak Bileşenler (shared.tsx)
        PageHeader
        Btn
        MetricCard
        Card
        CardHeader
        Chip
        ProgressBar
        CpiGauge
        ChartTooltipContent
    end

    DashboardScreen --> PageHeader
    DashboardScreen --> Btn
    DashboardScreen --> MetricCard
    DashboardScreen --> Card
    DashboardScreen --> CardHeader
    DashboardScreen --> Chip
    DashboardScreen --> ChartTooltipContent

    BudgetScreen --> PageHeader
    BudgetScreen --> Btn
    BudgetScreen --> Card
    BudgetScreen --> CardHeader
    BudgetScreen --> Chip

    EVMScreen --> PageHeader
    EVMScreen --> Btn
    EVMScreen --> MetricCard
    EVMScreen --> Card
    EVMScreen --> CardHeader
    EVMScreen --> CpiGauge

    HakedisScreen --> PageHeader
    HakedisScreen --> Btn
    HakedisScreen --> MetricCard
    HakedisScreen --> Card
    HakedisScreen --> CardHeader
    HakedisScreen --> Chip
```

---

## 3. Ekranlara Göre Alt Bileşen Kullanımı

### A. Dashboard Ekran Yapısı
*   **KPI Kartları**: 10 adet `MetricCard` (BAC, PV, AC, EV, EAC, CV, SV, CPI, SPI, VAC).
*   **Grafik**: Recharts `BarChart` (BAC, PV, AC, EV, EAC karşılaştırması).
*   **Tablo**: Projelerin kümülatif EVM performans listesi tablosu.
*   **Bildirimler**: `Card` ve `CardHeader` içinde listelenen uyarı listesi.

### B. Grafik Component Yapısı
Grafiklerde Recharts bileşenleri kurumsal açık tema paletiyle özelleştirilmiştir:
*   `ResponsiveContainer` (Esnek genişlik)
*   `AreaChart`, `BarChart`, `LineChart`, `ComposedChart`
*   `XAxis` / `YAxis` (Stil rengi: `#64748b`, font boyutu: `11px`)
*   `CartesianGrid` (Izgara çizgileri: `#e2e8f0`)
*   `ChartTooltipContent` (Folkart temalı tooltip kartı)

### C. Tablo Yapısı
Tablolarda harici bir kütüphane kullanılmamış, standart HTML `<table>` yapıları Tailwind sınıflarıyla açık temaya uyarlanmıştır.
*   `bg-slate-50` tablo başlığı arka planı.
*   `border-slate-200` kenarlıklar.
*   `hover:bg-blue-50/30` satır vurgusu.
*   Hücrelerde `font-mono` yazı biçimi (sayısal veriler için).

### D. Upload / Import Bileşenleri
*   `DataUploadScreen` altında sürükle-bırak paneli (`handleDrop` ve `handleDragOver` olaylarıyla).
*   Dosya seçimi için gizli `<input type="file">` ve onu tetikleyen `Btn`.
*   Yüklenen verilerin ilk 10 satırını gösteren önizleme tablosu.

---

## 4. Phase 2'de Yeniden Kullanılabilecek Bileşenler

*   `PageHeader` & `Btn`: Yeni eklenecek olan bütçe sihirbazı ve varsayım yönetim ekranlarında tutarlı başlıklar ve butonlar oluşturmak için.
*   `MetricCard`: Nakit akışı ve avans modüllerinde planlanan/gerçekleşen özet tutarları göstermek için.
*   `Card` & `CardHeader`: Yeni form ve parametre ayar pencerelerini çevrelemek için.
*   `Chip`: Yeni hakediş ve bütçe tiplerinin durumlarını (Onaylandı, Onay Bekliyor, Taslak vb.) göstermek için.

---

## 5. Dikkat Edilmesi Gereken Kırılgan Bileşenler

*   **`BudgetScreen` Tablosu**: `App.tsx` içinde WBS ağacını dinamik olarak inşa eder. WBS düzeyine göre satır açma/kapatma (`expanded` set yapısı) ve ay bazında planlanan/gerçekleşen metrajları hesaplayan döngüleri barındırır. Son derece karmaşıktır. Bu tablodaki en ufak değişiklik WBS hiyerarşisinin bozulmasına yol açabilir.
*   **`DashboardScreen` Performans Özeti**: Proje performans listesinde satır bazında `calculateEVM` hesaplamalarını tetikler. Sütun genişlikleri katı piksel değerleriyle (`colgroup` tanımları) kilitlenmiştir. Kolon ekleme/çıkarma durumunda tablo düzeni kayabilir.
