# Katkı Kılavuzu (Contributing Guide)

Düzce FENTEK Ödev Destek projesine katkıda bulunmak istediğiniz için teşekkür ederiz!
Bu belge, geliştirme ortamının kurulması, testlerin yürütülmesi ve Pull Request (PR) süreçleri için yol gösterir.

## Geliştirme Ortamı Kurulumu

1. **Gereksinimler:**
   - Node.js >= 22 (gömülü `node:sqlite` desteği zorunludur; Node 24 önerilir).
   - Git.

2. **Kurulum Adımları:**
   ```bash
   # Bağımlılıkları kurun
   cd backend
   npm ci

   # Örnek konfigürasyonu kopyalayın
   cp .env.example .env
   ```
   `.env` dosyası içinde `JWT_SECRET` değerinin tanımlı olduğundan emin olun.

3. **Yerel Sunucuyu Başlatma:**
   ```bash
   # Windows üzerinden başlatıcı ile:
   .\baslat-backend.bat -SadeceKontrol   # Yalnız kontrolleri denetler
   .\baslat-backend.bat                 # Sunucuyu başlatır ve tarayıcıyı açar

   # veya doğrudan Node ile:
   cd backend
   npm start
   ```

## Test ve Doğrulama Standartları

Her değişiklik öncesinde ve sonrasında tüm otomatik testler ve regresyon benchmarkları çalıştırılmalıdır:

```bash
cd backend

# Tüm testleri (59 çekirdek + 42 video + 19 kütüphane) çalıştırın:
npm run test:all

# Bireysel test grupları:
npm test              # Çekirdek uçtan uca akış testleri
npm run test:video    # Video, medya ve karantina testleri
npm run test:kutuphane # Kütüphane, arama ve öneri testleri

# AI moderasyon ve etik kural değerlendirmesi:
npm run eval
node ai-eval/bagimsiz-kos.js
node ai-eval/genis-kos.js
```

> [!IMPORTANT]
> Hiçbir PR, `npm run test:all` ve `npm run eval` testleri tam yeşil geçmeden birleştirilemez.

## Kodlama ve Güvenlik Kuralları

- **Temiz Kod & SOLID:** Modüler, okunabilir, tip güvenliğine ve tek sorumluluk prensibine uygun kod yazın.
- **Hassas Veri Yasağı:** `.env`, `*.db`, `*.db-wal`, `*.db-shm`, video dosyaları veya kişisel kullanıcı verilerini asla git geçmişine eklemeyin.
- **Geriye Dönük Uyumluluk:** Veritabanı şeması değişikliklerinde mevcut veriyi bozmayacak şekilde `ALTER TABLE ... ADD COLUMN` veya `CREATE TABLE IF NOT EXISTS` yaklaşımı uygulayın.
- **Hata Yönetimi ve Türkçe Mesajlar:** API uç noktalarında açık ve anlaşılır Türkçe hata yanıtları (`{ hata: "..." }`) döndürün.

## PR ve Commit Süreci

1. Yeni bir özellik veya hata düzeltmesi için anlamlı bir dal (branch) açın: `git checkout -b ozellik/yeni-filtre`.
2. Yapılan değişikliklere ait yeni regresyon testleri ekleyin.
3. Commit mesajlarında net ve açıklayıcı ifadeler kullanın.
4. PR açarken [PULL_REQUEST_TEMPLATE.md](file:///.github/PULL_REQUEST_TEMPLATE.md) şablonundaki alanları doldurun.
