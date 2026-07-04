# Folkart Kurumsal İnşaat Proje Platformu — Ortak Çalışma Rehberi

Bu depo Claude Code, Codex ve Antigravity tarafından ortak geliştirilecektir. Bu dosya tüm ajanlar için ortak çalışma sözleşmesidir.

## Proje amacı

Mevcut çalışan prototipi koruyarak Folkart'a ait, açık temalı bir kurumsal inşaat proje platformuna dönüştürmek. Platform; portföy, bütçe/WBS, kazanılmış değer, sapma, hakediş, S-eğrisi, nakit akışı, rapor, veri yükleme, kullanıcı, sözlük ve kaynak listesi ekranlarını tutarlı bir ürün çatısı altında sunacaktır.

## Değişmez kurallar

- Koyu tema kullanılmayacak; yeni tasarım açık ve kurumsal olacaktır.
- Mevcut yapı önce korunacak, dönüşüm küçük ve geri alınabilir adımlarla yapılacaktır.
- Firebase deploy uyumluluğu bozulmayacak. Vite'ın `dist/` çıktısı ve SPA çalışma biçimi korunacaktır.
- Risk, Monte Carlo ve Hedge bu aşamanın kapsamı dışındadır; bunlarla ilgili ekran, model veya bağımlılık eklenmeyecektir.
- Uygulama verileri şu anda mock veridir. Gerçek backend varmış gibi varsayım yapılmayacaktır.
- Her değişiklik grubundan sonra `npm run build` çalıştırılacaktır.
- Build başarılı olmadan hiçbir görev tamamlandı olarak işaretlenmeyecek ve “bitti” denmeyecektir.
- Kullanıcı istemeden kapsam genişletilmeyecek, geniş refactor veya bağımlılık değişimi yapılmayacaktır.
- Ajanlar kullanıcıyla Türkçe iletişim kurar; analiz, açıklama, plan, rapor ve yönlendirmeler Türkçe yazılır. Kod, dosya adı, fonksiyon adı, değişken adı, teknik terim, API ismi ve commit mesajları gerektiğinde İngilizce kalabilir.
- Kullanıcı arayüzünü veya deneyimini etkileyen her geliştirmede önce tasarım/plan kullanıcıya sunulur (ayrıntı: "UI/UX Geliştirme Kuralı").
- Hiçbir göreve doğrudan kod yazarak başlanmaz; önce ilgili dosyalar okunur, mevcut durum ve bağımlılıklar analiz edilir, plan çıkarılır.
- Hiçbir varsayım doğru kabul edilmez; hesaplamalar, formüller ve veri akışları test edilerek doğrulanır. Finansal hesaplamalar (EVM, bütçe, forecast, nakit akışı vb.) PMBOK ve `PROJECT_ROADMAP.md` BÖLÜM 3 EVM kurallarıyla tam uyumlu olmalıdır.

## Ajan çalışma akışı

Her görevde şu döngü eksiksiz uygulanır: **Analiz → Doğrula → Planla → Uygula → Test Et → Dokümantasyonu Güncelle → Tekrar Doğrula → Commit Öner.** Bir iş yalnızca "çalışıyor" göründüğü için tamamlanmış sayılmaz.

1. Oturuma başlamadan önce `AGENTS.md`, ilgili ajan talimatı, `PROJECT_ROADMAP.md`, `TASKS.md`, `CHANGELOG.md`, `PROJECT_ANALYSIS.md` ve `.claude/MEMORY.md` okunur.
2. `TASKS.md` içinden tek ve açık bir görev seçilir; aynı alan üzerinde çalışan başka bir ajan varsa çakışma önlenir.
3. Yeni özellik eklemeden önce mevcut kod, roadmap ve dokümantasyonla karşılaştırılır. Eksik, hatalı veya çelişkili nokta tespit edilirse önce kullanıcıya raporlanır; onay alınmadan geliştirmeye başlanmaz.
4. Uygulama planı kullanıcıya sunulur; büyük refactor, mimari değişiklik veya kapsam değişikliği içeren her adım için ayrıca onay alınır.
5. Mevcut davranış ve Firebase build çıktısı korunarak, onaylanan plan kapsamında mümkün olan en küçük değişiklik yapılır.
6. `npm run build` çalıştırılır; lint ve mevcut testler varsa onlar da çalıştırılır. Hata varsa görev tamamlanmaz; hata ve bağlam belgelenir.
7. Başarılı doğrulama sonrasında `TASKS.md`, `CHANGELOG.md` ve gerekli devralma notları güncellenir; dokümantasyonun kodla senkron kaldığı tekrar kontrol edilir.
8. Teslim notunda değişen dosyalar, doğrulama sonucu, kalan riskler ve sıradaki güvenli adım açıkça yazılır; commit önerisi bu notla birlikte sunulur (bkz. "Commit Öncesi Kontrol Listesi").

## Belge sorumlulukları

- `AGENTS.md`: bu projenin anayasa belgesidir — tüm ajanlar için kalıcı kurallar, çalışma metodolojisi ve mimari çerçeve. Her oturum bu dosyanın okunmasıyla başlar.
- `CLAUDE.md`: Claude Code'a özel çalışma talimatları; ortak kuralların yerine geçmez.
- `PROJECT_ROADMAP.md`: uzun vadeli vizyon, hedef mimari ve aşama planı (Aşama 1–12). Kural değil, yön belgesidir.
- `TASKS.md`: güncel öncelik, yapılacaklar ve tamamlanan işler için tek kaynak.
- `CHANGELOG.md`: doğrulanmış değişikliklerin kronolojik kaydı.
- `PROJECT_ANALYSIS.md`: depo analiz geçmişi; yeni analizler sona eklenir, önceki kayıtlar silinmez.
- `.claude/MEMORY.md`: oturumlar arası kısa durum ve devralma özeti.

Bu dosyalar arasında çelişki veya eksiklik tespit edilirse önce kullanıcıya raporlanır; onay alınmadan hiçbiri güncellenmez.

## UI/UX Geliştirme Kuralı

> **Araç notu:** Bu ortamda ayrı bir "Fable" UI/UX tasarım aracı **bağlı değildir** — yalnızca
> `claude-fable-5` adında bir model vardır, tasarım uygulaması değildir. Bağlı gerçek tasarım
> araçları Figma MCP ve Canva/Gamma'dır. Bu nedenle aşağıdaki süreç Fable yerine kod-içi
> tasarım + (istenirse) Figma MCP ile yürütülür. Bu not, önceki bir talimatın yanlış bir araç
> varsayımına dayandığı tespit edildiği için eklendi.

Yeni bir ekran, dashboard, sayfa, component, kullanıcı akışı, form, rapor, grafik veya kullanıcı deneyimini etkileyecek herhangi bir geliştirmeden önce şu soru sorulur: **"Bu geliştirme kullanıcı arayüzünü veya kullanıcı deneyimini etkiliyor mu?"**

Cevap "Evet" ise:

1. İhtiyaç analiz edilir; mevcut ekranların tasarım dili (renk, spacing, tablo/kart kalıpları) incelenir.
2. Tasarım, mevcut tasarım diline sadık kalınarak doğrudan kodda (veya istenirse önce Figma MCP ile görsel taslak olarak) oluşturulur.
3. Küçük/orta ölçekli tasarım kararları (kolon düzeni, responsive/scroll stratejisi vb.) ajan tarafından verilir; sonuç kullanıcıya checkpoint raporunda özetlenir.
4. Görünür, geri dönüşü zor veya roadmap ile çelişen bir tasarım kararı varsa uygulamadan önce kullanıcıya sorulur.
5. Kod, mevcut tasarım sistemiyle (bileşenler, renkler, tablo/kart kalıpları) birebir uyumlu olacak şekilde geliştirilir.

Yalnızca backend geliştirmeleri, algoritmalar, API entegrasyonları, performans optimizasyonları, testler, refactor ve UI'ı etkilemeyen teknik değişiklikler bu sürecin tamamı dışında doğrudan kodlanabilir.

## Kod Kalitesi İlkeleri

- Performans, sürdürülebilirlik, okunabilirlik, ölçeklenebilirlik ve kurumsal kalite standartları önceliklidir; geçici çözüm yerine kalıcı çözüm üretilir.
- Bu, "Değişmez kurallar"daki küçük-adım ve kapsam sınırlama ilkeleriyle birlikte uygulanır — kalıcı çözüm, büyük refactor için otomatik onay anlamına gelmez.

## Commit Öncesi Kontrol Listesi

Commit önerilmeden önce aşağıdakilerin tamamı doğrulanır:

- [ ] Build başarılı (`npm run build`).
- [ ] Testler başarılı (varsa).
- [ ] Lint hatası yok (varsa lint scripti).
- [ ] Yeni hata/regresyon oluşmadı.
- [ ] Kod, `PROJECT_ROADMAP.md` ve mevcut mimariyle uyumlu.
- [ ] `TASKS.md`, `CHANGELOG.md` ve ilgili diğer belgeler güncel.
- [ ] Gerekli dosyalar (kod + dokümantasyon) senkronize.

## Oturum Sonu Yönetici Özeti

Her oturum, aşağıdaki başlıkları içeren kısa bir Türkçe özetle kapanır:

- Tamamlanan işler
- Devam eden işler
- Tespit edilen riskler
- Sonraki önerilen adım

## Mevcut teknoloji ve mimari

- React 18.3.1 + TypeScript/TSX
- Vite 6.3.5 ve `@vitejs/plugin-react`
- Tailwind CSS 4 (`@tailwindcss/vite`)
- Recharts, Lucide React
- MUI/Emotion, Radix UI ve shadcn uyumlu UI bileşen dosyaları depoda mevcut; ana ekran bunların çoğunu henüz kullanmıyor.
- Uygulama girişi: `src/main.tsx`
- Ana prototip: `src/app/App.tsx`; ekranlar, ortak küçük bileşenler ve mock veriler tek dosyada.
- Stil giriş noktası: `src/styles/index.css`
- Production çıktısı: `dist/`

## Bilinen teknik durum

- `App.tsx` ~4.930 satırlık monolitik bir prototiptir (2026-06-28'de ~2.100 satırdı; WBS/Excel/EVM işiyle büyüdü) ve 13 ekranı local state ile değiştirir; URL tabanlı routing kullanılmamaktadır.
- 13 ekranın tamamı açık (Folkart) temaya geçirilmiştir (LOOP-01, 2026-06-28); görünür CONSTRUX metni kalmamıştır.
- Excel içe/dışa aktarım (SheetJS/`xlsx`) ve merkezi EVM motoru (`src/app/evmEngine.ts`) mevcuttur; tüm canlı CPI/SPI/EAC/CV/SV hesaplamaları bu motora yönlendirilmiştir (2026-07-02).
- Veri kalıcılığı, kimlik doğrulama ve gerçek API/Firebase entegrasyonu yoktur.
- Firebase yapılandırma dosyaları şu an depoda görünmemektedir; buna rağmen Vite production build'i `dist/` üretmektedir.
- Ana JavaScript paketi build sırasında ~1.169 kB (gzip ~343 kB) üretmekte ve Vite chunk-size uyarısı vermektedir; bu hata değildir, sonraki optimizasyon konusudur (bkz. `PROJECT_ANALYSIS.md`).

## İlk güvenli geliştirme ilkesi

İlk uygulama değişikliği; veri modellerine ve ekran davranışlarına dokunmadan semantik açık tema tokenlarını tanımlamak, ardından yalnızca uygulama kabuğu ve ortak bileşenleri bu tokenlara geçirmek olmalıdır. Her küçük adım production build ve görsel smoke test ile doğrulanmalıdır.
