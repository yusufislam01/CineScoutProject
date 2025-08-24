# CineScoutProject
Jetpack Compose + Clean Architecture + MVVM + Hilt + Room + Retrofit ile film keşif uygulaması
Amaç: Android ile modern mimariyi, testleri, CI/CD’yi ve iyi UI/UX pratiklerini uçtan uca deneyimlemek.

Proje Kuralları & GitHub Zorunluluğu
Bu proje GitHub’da barınmalıdır.
Depo public olabilir
Ana dal: main (korumalı); geliştirme dalları: feature/*, fix/*.
Tüm işlerin takibi GitHub Issues ve Projects ile yapılır.
Her görev için PR açılır, kod gözden geçirme (code review) zorunludur.
Build, test, lint/detekt kontrolleri PR üzerinde zorunlu çalışır.



TEKNİK YIĞIN
Dil: Kotlin (JDK 17+ önerilir)
UI: Jetpack Compose, Material 3, Navigation-Compose
DI: Hilt
Async: Coroutines, Flow
Ağ: Retrofit (OkHttp, Moshi/Gson)
Veritabanı: Room
Ayarlar: DataStore (Preferences)
Görseller: Coil
Test: JUnit5/4, MockK, Turbine, AndroidX Test, Compose UI Test

API: The Movie Database API



Ekranlar
1) ONBOARDING / İLK AÇILIŞ
Amaç: Uygulamanın değerini anlatmak, tema seçimini (Sistem/Koyu/Açık) ve opsiyonel izinleri (bildirim/analitik) almak.

Bileşenler (Material 3 & Compose): - Pager (3 adım önerilir) - LargeTopAppBar (opsiyonel, sade tasarım tercih) - Görsel/İllüstrasyon alanı - CTA: Button → “Başla” - Tema seçimi: SegmentedButtons veya RadioGroup - Alt bilgi: gizlilik & lisans linkleri

Durumlar: - Normal akış (ilk kurulum) - Offline uyarısı (ops.) - İzin reddi (ops., “daha sonra” butonu)

Kabul Kriterleri: - Kullanıcı “Başla” ile onboarding’i tamamlar ve bir daha gösterilmez (DataStore flag). - Tema seçimi kalıcıdır ve uygulama genelinde uygulanır. - (Ops.) İzin isteme adımı atlanabilir veya daha sonra ayarlardan açılabilir.

Erişilebilirlik: - Tüm CTA’lar ≥ 48dp dokunma alanı - İllüstrasyonlara anlamlı contentDescription (ya da dekoratifse boş) - Kontrast ve dinamik renk desteği



2) HOME (POPÜLER / TREND)
Amaç: Popüler ve trend filmleri sekmelerle listelemek; sonsuz kaydırma ile içerik keşfini kolaylaştırmak.

Bileşenler: - TopAppBar (logo + arama ikonu) - TabRow → Popüler / Trend - İçerik ızgarası: 2 sütun (telefon), 3–4 sütun (tablet) - MovieCard: Poster (2:3), başlık, puan rozeti - LazyVerticalGrid + Paging 3 - Snackbar (hata/geri alma) - Pull-to-refresh (ops.)

Durumlar: - Yükleniyor: iskelet kartlar / shimmer - Boş: “İçerik bulunamadı” + Yeniden dene - Hata: Açıklayıcı mesaj + Retry - Online/Offline: offline’da önbellek (varsa) göster

Kabul Kriterleri: - Sekme değişiminde ilgili liste ayrı Paging akışıyla gelir. - Sonsuz kaydırma ve retry davranışı çalışır. - Kart tıklaması Detail(movieId) ekranına yönlendirir. - Hata/boş durumları görsel olarak ayırt edilir ve kullanıcıya tek dokunuşla çözüm sunulur.

Erişilebilirlik: - Poster contentDescription başlık bilgisi içerir. - Sekme durumları (selected/unselected) ekran okuyucuya duyurulur.



3) SEARCH (ARAMA)
Amaç: Serbest metin arama yapma; geçmiş aramaları kısayol olarak sunma; sonuçları sayfalamayla gösterme.

Bileşenler: - SearchBar (debounce: 300–500ms) - “Son aramalar” AssistChip/SuggestionChip listesi - Sonuç listesi: LazyColumn/LazyVerticalGrid + Paging 3 - MovieRow/MovieCard (liste/ızgara tercihine göre) - “Filtre temizle”/“Yeniden dene” butonları (durumlara göre)

Durumlar: - Input boş: Son aramalar / öneriler - Yükleniyor: iskelet listeler - Sonuç yok: “Sonuç bulunamadı” + ipucu - Hata: “Bağlantı sorunu” + Retry

Kabul Kriterleri: - Kullanıcı yazarken debounce ile istek sayısı kontrollü artar. - Son aramalar DataStore’da saklanır; chip’e dokununca arama alanına set edilir ve sonuçlar gelir. - Sonuç kartına dokununca Detail(movieId) açılır. - Boş/hata durumları uygun mesaj ve eylemle yönetilir.

Erişilebilirlik: - SearchBar odak (focus) durumunda klavye erişimi sorunsuz. - Sonuç sayısı değişimleri ekran okuyucuya duyurulur (ops. liveRegion).




4) DETAIL (FILM DETAYI)
Amaç: Filmin ayrıntılarını (poster, başlık, türler, özet, puan, oyuncu/ekip) göstermek ve favori ekleme/çıkarma akışını sunmak.

Bileşenler: - LargeTopAppBar (çökme davranışlı) - Poster başlık alanı (arka plan görseli ops.) - Bilgi bölümü: başlık, yıl, tür FilterChip/AssistChip, puan rozeti - Özet (ExpandableText ops.) - Oyuncular yatay liste (LazyRow): küçük avatar + isim/rol - Favori IconToggleButton (kalp) - Fragman dış bağlantısı (ops.) - Paylaş butonu (ops.)

Durumlar: - Yükleniyor: detay iskeleti + cast placeholder - Hata: “Yüklenemedi” + Retry - Offline: Daha önce görüntülendiyse önbellekten kısmi veri göster

Kabul Kriterleri: - movieId ile ekran açılır; veri geldikçe UI kademeli dolar. - Favori toggle, Room’a yazılır ve Favorites ekranında anında görünür. - Offline’da önbellekte veri varsa sunulur; yoksa uygun mesaj gösterilir. - Derin link (ops.): cinescout://movie/{id} çalışır.

Erişilebilirlik: - Poster ve butonlar için anlamlı contentDescription. - Uzun metin (özet) satır sonlarında kısaltma … ve “Daha fazla/gizle” erişilebilir.



TASARIM
Ekran tasarımları için AI toollar kullanılabilir
Ekran bazlı AI prompt şablonları,

Home (Popular/Trending)
“Design an Android Material 3 Home screen with two tabs (Popular/Trending), a TopAppBar with logo and search icon, a 2-column movie card grid with poster (2:3), title, rating pill, infinite scroll placeholders, and error/empty states.”

Search
“Create a Search screen with a floating SearchBar, recent searches chips, result list with posters, and states: empty (before typing), loading, no results with retry.”

Detail
“Design a Movie Detail screen using LargeTopAppBar (collapsing), big poster header, title, genres chips, synopsis paragraph, rating pill, horizontal cast list, Favorite toggle, and a section divider system.”

Favorites
“Design a Favorites screen with grid/list toggle, empty state illustration with CTA to browse Popular, and swipe/long-press to remove behavior hints.”

Settings
“Design a Settings screen with theme selection (System/Dark/Light), a ‘Clear cache’ button, ‘About & Licenses’ link, using Material 3 lists and switches.”



🛠 TASARIM AI TOOL ÖNERİSİ
Figma free plan aç.
Material 3 Design Kit (Google resmi) dosyasını kopyala.
Figma AI beta’ya erişimin varsa, ekranları prompt ile üret (yoksa Uizard veya Galileo AI ile konsept çıkar → Figma’ya taşı).
Figma’da M3 komponentleriyle prompt’tan gelen tasarımı yeniden kur → spacing, typography, renkleri M3 token’larıyla ayarla.



📌 FIGMA AI İÇİN ÖRNEK TEK PROMPT (4 EKRAN BİRDEN)
“Design a Material 3 Android app called CineScout with the following screens: 
1) Onboarding (3-step pager with illustration, theme selection, start button),
2) 2) Home (top app bar with logo, tabs Popular/Trending, movie card grid 2:3 ratio, infinite scroll),
3) 3) Search (search bar, recent search chips, results grid, empty/error states),
4) 4) Detail (collapsing top app bar, large poster, title, genres chips, rating pill, cast carousel, favorite toggle). Use 8dp grid, accessible touch targets, light and dark variants.”
