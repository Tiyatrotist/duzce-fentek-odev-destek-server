# Düzce FENTEK Ödev Destek

[![CI](https://github.com/Tiyatrotist/duzce-fentek-odev-destek-server/actions/workflows/ci.yml/badge.svg)](https://github.com/Tiyatrotist/duzce-fentek-odev-destek-server/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Node.js Version](https://img.shields.io/badge/node-%3E%3D22.0.0-brightgreen.svg)](https://nodejs.org/)

Okul ve eğitim ortamları için tasarlanmış; soru-cevap, ders paylaşımları, kişisel notlar, çalışma programı, çok katmanlı etik moderasyon, kütüphane/öneri sistemi ve karantina korumalı video çözüm akışları sunan yerel odaklı bir mini sosyal ağ platformudur.

---

## Proje Nedir ve Ne Değildir?

- **Nedir?**
  - Okul ağında yerel veya kontrollü sunucuda çalışabilen, veri egemenliğini koruyan bir eğitim paylaşım ve destek uygulamasıdır.
  - Öğrencilerin soru sorabildiği, onaylı öğretmenlerin yanıtlayabildiği, çalışma programı ve deneme takibi yapabildiği bir platformdur.
  - Sınav sızıntısı, kopya ve zorbalık gibi durumlara karşı kural tabanlı + benzerlik + şifreli havuz katmanlarıyla önleyici denetim sunan bir mimaridir.
- **Ne değildir?**
  - Milyonlarca kullanıcıya yönelik dağıtık bir genel bulut sosyal medyası değildir.
  - İnsan denetimini tamamen ortadan kaldıran veya yapay zekaya tek başına ceza verme yetkisi tanıyan bir "otonom polis" değildir.

## Temel Yetenekler ve Mimari Özeti

- **RESTful API & SSE Akışı:** Express 4 tabanlı JSON API ve yapay zeka asistanı için Server-Sent Events (SSE) akış desteği.
- **Gömülü Veritabanı (Zero External Dependency):** Harici veritabanı kurulumu gerektirmeyen, yüksek performanslı ve WAL (Write-Ahead Logging) modunda çalışan yerel SQLite (`node:sqlite`).
- **Çok Katmanlı Moderasyon Hattı:**
  - *Katman 1:* Regex ve leksikal kurallar (`config/etik.js`).
  - *Katman 2:* TF-IDF benzerlik hesaplaması (`ai/benzerlik.js`).
  - *Katman 3:* AES-256-GCM ile şifrelenmiş öğretmen sınav soru havuzu (`ai/havuz.js`).
  - *Katman 4:* İsteğe bağlı LLM sınıflandırma adaptörü (`ai/llm.js`).
- **Video Çözüm & Karantina Boru Hattı:** Dosya baytlarından MP4/WebM doğrulaması, öngörülemez anahtarlarla izole depolama, dayanıklı arka plan iş kuyruğu ve HTTP 206 Partial Content (Range) desteği.
- **Anti-Bot & Spam Koruması:** Ortak okul IP'lerini cezalandırmayan, kullanıcı ve işlem türü bazlı hız sınırları (`spam.js`) ile idempotency (`X-Istek-Id`) desteği.

Detaylı mimari şeması ve akışlar için [docs/architecture.md](docs/architecture.md) dokümanını inceleyin.

---

## Dizin Yapısı

- `backend/`: Node.js Express API sunucusu, SQLite veritabanı, AI moderasyon motoru, video kuyruğu ve testler.
- `odev-destek/`: Web kullanıcı arayüzü (HTML5, Vanilla JS, CSS, PWA).
- `docs/`: Mimari, geliştirme ve bakım kanıtı dokümantasyonu.
- `baslat-backend.bat` & `.ps1`: Windows yerel geliştirme ve başlatma otomasyonu.
- `EKSIKLER.md`: Doğrulanmış eksikler ve sistem sınırları günlüğü.

---

## Hızlı Başlangıç (Quick Start)

### 1. Gereksinimler
- **Node.js**: Sürüm >= 22 (gömülü `node:sqlite` desteği için gereklidir; v24 LTS önerilir).
- **FFmpeg**: (Opsiyonel) Video çözümlerinden otomatik kare çıkarma ve ses analizi için.

### 2. Kurulum
```bash
# Projeyi klonlayın
git clone https://github.com/Tiyatrotist/duzce-fentek-odev-destek-server.git
cd duzce-fentek-odev-destek-server/backend

# Bağımlılıkları kurun
npm ci

# Örnek konfigürasyon dosyasını oluşturun
cp .env.example .env
```
`.env` dosyasını açıp `JWT_SECRET` anahtarını güçlü bir değerle güncelleyin.

### 3. Çalıştırma
```bash
# Windows tek tıkla başlatıcı ile:
..\baslat-backend.bat

# veya doğrudan Node ile:
npm start
```
Varsayılan adres: **http://127.0.0.1:3001**  
Sağlık kontrolü: **http://127.0.0.1:3001/api/saglik**  
Sistem hazırlık durumu: **http://127.0.0.1:3001/api/hazirlik**

---

## Testler ve Doğrulama

Backend testleri gerçek veritabanına (`odevagi.db`) dokunmaz; izole test defterlerinde çalışır:

```bash
cd backend

# Tüm test paketini (120 test) çalıştırın:
npm run test:all

# Bireysel test takımları:
npm test              # Çekirdek uçtan uca akış (59 test)
npm run test:video    # Video ve karantina testleri (42 test)
npm run test:kutuphane # Kütüphane, arama ve öneri testleri (19 test)

# AI moderasyon kıyaslamaları:
npm run eval          # 304 kurgusal Türkçe regresyon testi
node ai-eval/bagimsiz-kos.js
node ai-eval/genis-kos.js
```

---

## Güvenlik ve Gizlilik Yaklaşımı

- **Local-First & Least Privilege:** Sunucu varsayılan olarak yalnızca loopback (`127.0.0.1`) arayüzünü dinler.
- **Fail-Closed Tasarım:** Şifreli sınav havuzu anahtarı (`SINAV_KEY`) veya video moderasyon sağlayıcıları eksik olduğunda sistem içerikleri sessizce onaylamaz; insan incelemesine (`needs_review`) yönlendirir.
- **Hassas Veri İzolasyonu:** `.env`, `*.db`, video karantina deposu ve loglar `.gitignore` ile korunur.
- Güvenlik açığı bildirim süreci için [SECURITY.md](SECURITY.md) dosyasını inceleyin.

---

## Bilinen Sınırlar (Known Limitations)

- **AI Moderasyon:** Harici bir LLM API anahtarı girilmediğinde sistem Katman 1 (kural), Katman 2 (TF-IDF) ve Katman 3 (şifreli havuz) ile deterministik modda çalışır.
- **Kare Örneklemesi:** Video moderasyonunda kare örnekleme yöntemi anlık kısa kareleri kaçırabilir; şüpheli veya düşük güvenli içerikler her zaman insan moderatör onayına bırakılır.
- **E-Posta Gönderimi:** Şifre sıfırlama işlemi varsayılan olarak SMTP sunucusuna bağlı değildir; yönetici onayıyla gerçekleştirilir.

---

## Katkıda Bulunma ve Lisans

- Katkı kuralları, branch akışı ve PR beklentileri için [CONTRIBUTING.md](CONTRIBUTING.md) dosyasını okuyun.
- Değişiklik geçmişi için [CHANGELOG.md](CHANGELOG.md) dosyasına bakın.
- Bu proje [MIT Lisansı](LICENSE) altında lisanslanmıştır.
