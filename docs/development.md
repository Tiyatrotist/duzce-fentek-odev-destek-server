# Geliştirici Kılavuzu (Development Guide)

Bu belge, Düzce FENTEK Ödev Destek projesinde geliştirme yapacak mühendisler için teknik adımları ve ortam ayarlarını açıklar.

## Gereksinimler

- **Node.js**: >= 22.0.0 (Proje `node:sqlite` gömülü modülünü kullanır; Node v24.12.0 LTS önerilir).
- **FFmpeg & FFprobe**: (Opsiyonel) Video çözümlerinden otomatik kare çıkarma ve ses analizi için PATH üzerinde bulunmalıdır.
- **Tesseract.js**: OCR testleri ve görsel metin analizi için npm bağımlılıklarında yer alır.

## Ortam Değişkenleri (Environment Variables)

`backend/.env.example` dosyasında bulunan temel değişkenler:

| Değişken | Varsayılan | Zorunlu? | Açıklama |
| :--- | :--- | :--- | :--- |
| `PORT` | `3001` | Hayır | HTTP sunucusunun dinleyeceği port |
| `HOST` | `127.0.0.1` | Hayır | Dinlenecek ağ adresi (güvenlik için loopback kalmalıdır) |
| `JWT_SECRET` | - | **Evet** | Oturum token'larını imzalamak için güçlü gizli anahtar |
| `SINAV_KEY` | - | Hayır | AES-256-GCM sınav havuzu şifreleme anahtarı |
| `URETIM` | `0` | Hayır | `1` ise seed devre dışı kalır, demo hesaplar engellenir |
| `KOKENLER` | - | `URETIM=1` ise evet | İzin verilen CORS origin listesi (virgüllü) |
| `DB_PATH` | `./odevagi.db` | Hayır | SQLite veritabanı dosya yolu |
| `VIDEO_STORE` | `./video-depo` | Hayır | Karantina video dosyalarının saklanacağı klasör |

## Test Çalıştırma Prosedürü

Testler production veritabanına dokunmaz; her test grubu ayrı test veritabanı (`./test-*.db`) ve ayrı port kullanır:

```bash
cd backend

# 1. Tüm test takımı
npm run test:all

# 2. Bireysel testler
npm test               # Port 3131: Çekirdek uçtan uca akış (59 test)
npm run test:video     # Port 3141: Video ve karantina testleri (42 test)
npm run test:kutuphane # Port 3142: Kütüphane, arama ve öneri testleri (19 test)

# 3. AI moderasyon kıyaslamaları
npm run eval           # 304 kurgusal Türkçe regresyon testi
node ai-eval/bagimsiz-kos.js # 41 bağımsız örnek
node ai-eval/genis-kos.js    # 72 kural tabanlı değerlendirme örneği
```

## Başlatıcı Betikleri (Windows)

- `baslat-backend.bat`: Çift tıklama ile Windows ortamında Node sürümünü, paketleri, `.env` dosyasını denetler ve sunucuyu başlatır.
- `baslat-backend.bat -SadeceKontrol`: Sunucuyu başlatmadan yalnızca ortam gereksinimlerini kontrol eder.
- `baslat-backend.bat -TarayiciAcma`: Sunucuyu başlatır ancak tarayıcıyı otomatik açmaz.
