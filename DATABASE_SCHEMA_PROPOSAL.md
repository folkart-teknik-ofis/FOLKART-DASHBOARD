# DATABASE_SCHEMA_PROPOSAL

Bu belgede Folkart Bütçe ve Maliyet Yönetim Platformu Phase 2 sonunda ilişkisel bir veritabanında (SQL/PostgreSQL) bulunması gereken tablo tasarımları, anahtar alanları, veri tipleri ve tablolar arası ilişkiler tanımlanmıştır.

---

## 1. Projeler Tablosu (`Projects`)
*   **Amacı**: Sisteme kayıtlı olan inşaat projelerinin künye bilgilerini tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `project_code` (VARCHAR, Unique) - Örn: "3022"
    *   `project_name` (VARCHAR) - Örn: "Nova"
    *   `project_type` (VARCHAR) - Örn: "Karma Proje"
    *   `start_date` (DATE)
    *   `planned_end_date` (DATE)
    *   `current_end_date` (DATE)
    *   `base_currency` (VARCHAR) - Örn: "TRY"
    *   `status` (VARCHAR) - active, onhold, completed, planning
    *   `manager_name` (VARCHAR)
    *   `construction_area` (NUMERIC) - İnşaat alanı m²
*   **İlişkiler**: 
    *   `BudgetVersions` tablosuyla bire çok (one-to-many) ilişkilidir.

---

## 2. Bütçe Sürümleri Tablosu (`BudgetVersions`)
*   **Amacı**: Projelere ait farklı bütçe tiplerinin versiyon bilgilerini ve onay durumlarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `project_id` (UUID, Foreign Key) - `Projects.id`'ye bağlı
    *   `version_type` (VARCHAR) - Fizibilite, Keşif, Master, Revize
    *   `version_no` (INTEGER) - Örn: 1, 2, 3
    *   `version_code` (VARCHAR) - Örn: "M1", "R2"
    *   `is_active` (BOOLEAN) - Aktif çalışan bütçe sürümü mü?
    *   `status` (VARCHAR) - Draft, Approved, Archived
    *   `created_by` (VARCHAR)
    *   `approved_at` (TIMESTAMP)
*   **İlişkiler**:
    *   `Projects` tablosuna bağlıdır.
    *   `BudgetLines` tablosuyla bire çok (one-to-many) ilişkilidir.

---

## 3. Bütçe Satırları Tablosu (`BudgetLines`)
*   **Amacı**: Bütçe sürümlerinin içerdiği WBS kırılımlı her bir iş kalemi satırını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `budget_version_id` (UUID, Foreign Key) - `BudgetVersions.id`'ye bağlı
    *   `wbs_code` (VARCHAR) - Örn: "09.02.01" (Kaynak Kodu)
    *   `item_name` (VARCHAR) - Örn: "Kazı ve Hafriyat"
    *   `unit` (VARCHAR) - Örn: "m3", "Lt", "Tutar"
    *   `quantity` (NUMERIC) - Planlanan Metraj
    *   `unit_price` (NUMERIC) - Planlanan Birim Fiyat
    *   `currency` (VARCHAR) - Orijinal Satır Para Birimi (TRY, USD, EUR)
    *   `base_amount` (NUMERIC) - Metraj * Birim Fiyat (Orijinal para birimi)
    *   `distribution_method` (VARCHAR) - Linear, BellCurve, Manual
    *   `payment_term_id` (UUID, Foreign Key) - `PaymentTerms.id`'ye bağlı
*   **İlişkiler**:
    *   `BudgetVersions` ve `PaymentTerms` tablolarına bağlıdır.
    *   `Assumptions` tablosuyla bire bir/çok ilişkilidir.

---

## 4. Bütçe Varsayımları Tablosu (`Assumptions`)
*   **Amacı**: Bütçe satırlarında hangi çarpanların (kur, enflasyon vb.) aktif olduğunu tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `budget_line_id` (UUID, Foreign Key) - `BudgetLines.id`'ye bağlı
    *   `apply_exchange_rate` (BOOLEAN) - Kur çarpanı uygulansın mı?
    *   `apply_inflation` (BOOLEAN) - Enflasyon çarpanı uygulansın mı?
    *   `apply_construction_index` (BOOLEAN) - İnşaat endeksi uygulansın mı?
    *   `apply_commodity_index` (BOOLEAN) - Emtia çarpanı uygulansın mı?
*   **İlişkiler**:
    *   `BudgetLines` tablosuna bağlıdır.

---

## 5. Döviz Kurları Tablosu (`CurrencyRates`)
*   **Amacı**: Proje bazında aylık planlanan ve gerçekleşen USD/EUR kurlarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `project_id` (UUID, Foreign Key) - `Projects.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `currency` (VARCHAR) - USD, EUR
    *   `planned_rate` (NUMERIC) - Planlanan kur (TL karşılığı)
    *   `actual_rate` (NUMERIC) - Gerçekleşen kur (TL karşılığı)
*   **İlişkiler**:
    *   `Projects` tablosuna bağlıdır.

---

## 6. Enflasyon Oranları Tablosu (`InflationRates`)
*   **Amacı**: Proje bazında aylık planlanan ve gerçekleşen enflasyon oranlarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `project_id` (UUID, Foreign Key) - `Projects.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `currency` (VARCHAR) - TRY, USD, EUR
    *   `planned_rate` (NUMERIC) - Aylık planlanan enflasyon %
    *   `actual_rate` (NUMERIC) - Aylık gerçekleşen enflasyon %
*   **İlişkiler**:
    *   `Projects` tablosuna bağlıdır.

---

## 7. Emtia Endeksleri Tablosu (`CommodityIndexes`)
*   **Amacı**: Bütçe çarpanı olarak kullanılacak emtia (demir, çimento vb.) aylık katsayılarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `project_id` (UUID, Foreign Key) - `Projects.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `commodity_type` (VARCHAR) - Steel, Cement, Copper
    *   `planned_factor` (NUMERIC) - Planlanan artış çarpanı
    *   `actual_factor` (NUMERIC) - Gerçekleşen artış çarpanı
*   **İlişkiler**:
    *   `Projects` tablosuna bağlıdır.

---

## 8. İnşaat Maliyet Endeksleri Tablosu (`ConstructionIndexes`)
*   **Amacı**: İnşaat maliyet endeksi (TÜİK vb.) planlanan ve gerçekleşen aylık çarpanlarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `project_id` (UUID, Foreign Key) - `Projects.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `planned_factor` (NUMERIC) - Planlanan endeks katsayısı
    *   `actual_factor` (NUMERIC) - Gerçekleşen endeks katsayısı
*   **İlişkiler**:
    *   `Projects` tablosuna bağlıdır.

---

## 9. Nakit Akış Tablosu (`CashFlow`)
*   **Amacı**: Bütçe satırlarından üretilen aylık planlanan nakit çıkış planını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `budget_line_id` (UUID, Foreign Key) - `BudgetLines.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `planned_cash_out` (NUMERIC) - Planlanan Net Nakit Çıkışı (TL)
    *   `planned_kdv` (NUMERIC) - Planlanan KDV Tutarı
    *   `planned_withholding` (NUMERIC) - Planlanan Kesinti Tutarı
*   **İlişkiler**:
    *   `BudgetLines` tablosuna bağlıdır.

---

## 10. Avans Nakit Akış Tablosu (`CashFlowAdvance`)
*   **Amacı**: Taşeron avans ödemelerini ve bu avansların aylık hakedişlerden yapılacak mahsup planını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `contract_id` (UUID, Foreign Key) - `Contracts.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `advance_payment` (NUMERIC) - Yapılan/Planlanan Avans Ödemesi
    *   `advance_deduction` (NUMERIC) - Mahsup Edilen Avans Tutarı
    *   `remaining_advance_balance` (NUMERIC) - Kalan Avans Bakiyesi
*   **İlişkiler**:
    *   `Contracts` tablosuna bağlıdır.

---

## 11. Fiziki İlerleme Tablosu (`Progress`)
*   **Amacı**: İş kalemlerinin aylık fiziki gerçekleşme yüzdelerini ve kazanılmış değer (EV) oranlarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `budget_line_id` (UUID, Foreign Key) - `BudgetLines.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `physical_progress_pct` (NUMERIC) - Fiziki İlerleme Yüzdesi (Küm. %)
    *   `earned_value` (NUMERIC) - Kazanılmış Değer (EV) Tutarı
*   **İlişkiler**:
    *   `BudgetLines` tablosuna bağlıdır.

---

## 12. Gerçekleşen Maliyetler Tablosu (`ActualCosts`)
*   **Amacı**: Faturalardan veya muhasebeden gelen fiili gerçekleşen maliyetleri (AC) WBS düzeyinde tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `budget_line_id` (UUID, Foreign Key) - `BudgetLines.id`'ye bağlı
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `amount` (NUMERIC) - Gerçekleşen Maliyet (TL karşılığı)
    *   `original_amount` (NUMERIC) - Orijinal fatura tutarı
    *   `original_currency` (VARCHAR) - Fatura para birimi
    *   `exchange_rate_applied` (NUMERIC) - Uygulanan kur değeri
    *   `source` (VARCHAR) - Muhasebe (SAP), Hakediş, Manuel
*   **İlişkiler**:
    *   `BudgetLines` tablosuna bağlıdır.

---

## 13. Ödeme Vadeleri Tablosu (`PaymentTerms`)
*   **Amacı**: Bütçe ve sözleşmelerde kullanılacak vade öteleme kurallarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `term_name` (VARCHAR) - Örn: "30 Gün Vade", "Peşin", "60 Gün Vade"
    *   `delay_months` (INTEGER) - Öteleme Ay Sayısı (örn: 1, 0, 2)
*   **İlişkiler**:
    *   `BudgetLines` ve `Contracts` tablolarıyla ilişkilidir.

---

## 14. Sözleşmeler / Kontratlar Tablosu (`Contracts`)
*   **Amacı**: Taşeron ve yüklenicilerle yapılan resmi sözleşme detaylarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `project_id` (UUID, Foreign Key) - `Projects.id`'ye bağlı
    *   `contract_code` (VARCHAR, Unique)
    *   `contractor_name` (VARCHAR) - Yüklenici Ünvanı
    *   `total_amount` (NUMERIC) - Sözleşme Bedeli
    *   `currency` (VARCHAR)
    *   `advance_rate` (NUMERIC) - Avans Oranı %
    *   `advance_deduction_rate` (NUMERIC) - Hakedişten Avans Mahsup Oranı %
    *   `retention_rate` (NUMERIC) - Teminat Kesinti Oranı %
    *   `payment_term_id` (UUID, Foreign Key) - `PaymentTerms.id`'ye bağlı
*   **İlişkiler**:
    *   `Projects` ve `PaymentTerms` tablolarına bağlıdır.
    *   `ProgressPayments` tablosuyla bire çok (one-to-many) ilişkilidir.

---

## 15. Hakediş Ödemeleri Tablosu (`ProgressPayments`)
*   **Amacı**: Taşeronlara yapılan onaylı hakedişleri ve bu hakedişlerin WBS icmal detaylarını tutar.
*   **Ana Alanlar**:
    *   `id` (UUID, Primary Key)
    *   `contract_id` (UUID, Foreign Key) - `Contracts.id`'ye bağlı
    *   `payment_no` (VARCHAR) - Örn: "HK-001"
    *   `period` (VARCHAR) - Örn: "2024-09"
    *   `gross_amount` (NUMERIC) - Brüt Hakediş Tutarı
    *   `advance_deduction` (NUMERIC) - Kesilen Avans Mahsubu
    *   `retention_amount` (NUMERIC) - Teminat Kesintisi
    *   `kdv_amount` (NUMERIC) - KDV Tutarı
    *   `net_amount` (NUMERIC) - Ödenmesi gereken net tutar
    *   `status` (VARCHAR) - Approved, Pending, Disputed
    *   `approval_date` (DATE)
*   **İlişkiler**:
    *   `Contracts` tablosuna bağlıdır.
    *   Ayrıca WBS düzeyinde detayları tutmak için bir `ProgressPaymentDetails` (Hakediş İcmal Satırları) ilişkisi eklenebilir.
