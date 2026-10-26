# Değişiklik Günlüğü (Changelog)

Tüm önemli değişiklikler bu dosyada belgelenmektedir.
Format [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) prensiplerine ve [Semantic Versioning](https://semver.org/spec/v2.0.0.html) standardına dayanmaktadır.

## [1.0.0] - 2026-10-26

### Eklendi (Added)
- **Kapsamlı Test Altyapısı**: 59 çekirdek API/iş akışı, 42 video çözüm denetimi, 19 kütüphane ve öneri sistemi olmak üzere 120 otomatik uçtan uca test.
- **AI Moderasyon Kıyaslama Kümesi**: 304 regresyon örneği (`npm run eval`), 41 bağımsız örnek (`bagimsiz-kos.js`) ve 72 genişletilmiş kural tabanlı değerlendirme testi (`genis-kos.js`).
- **Gelişmiş .gitignore ve Güvenlik Hijyeni**: `.db`, `.db-wal`, `.db-shm`, `video-depo/`, `.env` ve tesseract `*.traineddata` dosyalarının takibi engellendi.
- **GitHub Actions CI Hattı**: Node 22 ve Node 24 üzerinde Ubuntu ve Windows matrix testleri (`.github/workflows/ci.yml`).
- **Açık Kaynak Standartları**: `LICENSE` (MIT), `CONTRIBUTING.md`, `SECURITY.md`, PR ve Issue şablonları.
- **Mimari ve Geliştirici Dokümantasyonu**: `docs/architecture.md`, `docs/development.md` ve `docs/oss-maintenance-evidence.md`.

### Düzeltildi (Fixed)
- **Test Ortamı JWT_SECRET**: `test.js` dosyasında bağımsız test ortamı için varsayılan JWT sırrı eklendi; harici `.env` bulunmasa dahi testlerin deterministik çalışması sağlandı.
- **SQLite WAL & SHM Temizliği**: Test öncesi ve sonrasında (`before` / `after`) `.db` dosyalarının yanında `-wal` ve `-shm` geçici yazma dosyalarının da temizlenmesi sağlandı.
- **Zod Güvenlik Güncellemesi**: `zod` paketi `^4.6.7` sürümüne güncellenerek GHSA-mrhx-c7xc-m3w5 bellek tüketimi zaafiyeti giderildi; `npm audit` 0 zafiyet seviyesine getirildi.
- **Body-Parser Güvenlik Güncellemesi**: DoS zaafiyeti içeren alt bağımlılık `npm audit fix` ile güncellendi.

### Güvenlik (Security)
- Sunucu dinleme varsayılanı `HOST=127.0.0.1` olarak teyit edildi.
- Video yüklemelerinde dosya bayt imzası (magic bytes) ve karantina zorunluluğu uygulandı.
- Öğretmen sınav havuzunda AES-256-GCM şifreleme ve fail-safe mekanizması korundu.
