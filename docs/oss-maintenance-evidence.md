# Açık Kaynak Bakım ve Güvenilirlik Kanıtı (OSS Maintenance Evidence)

Bu belge, projenin sürdürülebilir, doğrulanabilir ve profesyonel açık kaynak mühendislik standartlarına uygunluğunu kayıt altına alır.

## Maintainer ve Kimlik Bilgileri
- **Proje Sahibi / Maintainer**: Tiyatrotist
- **Git Commit İmzası**: `179411334+Tiyatrotist@users.noreply.github.com`
- **Tarih**: 2026-10-26

## Test Mühendisliği ve Doğrulama Kanıtları

Testler yerel ortamda ve CI hattında izole test veritabanları üzerinde sıfırdan koşturulmuştur:

```
Test Grupları                     Durum    Süre
--------------------------------------------------
test.js (Uçtan uca akış)         59/59    ~12s
test-video.js (Medya & Kuyruk)   42/42    ~4.5s
test-kutuphane.js (Arama & RAG)  19/19    ~3.0s
Toplam Otomatik Test:            120/120  ~19.5s (Tümü Yeşil)

AI Moderasyon Ölçümleri (npm run eval):
- SINAV_SIZINTISI: Precision %100, Recall %100, F1 %100
- CEVAP_ANAHTARI:  Precision %100, Recall %100, F1 %100
- KOPYA_YARDIMI:   Precision %100, Recall %100, F1 %100
- ZORBALIK_TACIZ:  Precision %100, Recall %100, F1 %100
- TEMIZ Yanlış Alarm Oranı: %0.0 (0/79)
- Bağımsız Değerlendirme (bagimsiz-kos.js): 41/41 doğru (%100)
- Geniş Kural Değerlendirme (genis-kos.js): 67/67 doğru (%100)
```

## Güvenlik Denetimleri
- `npm audit` çalıştırması: **0 zafiyet** (Zod `^4.6.7` güncellemesi ve body-parser düzeltmesi uygulandı).
- SQLite WAL modundaki geçici dosyaların (`-wal`, `-shm`) test sonrasında temizlenmesi sağlandı.
- `.gitignore` kapsamı genişletildi: veritabanı, medya karantinası, `.env` ve OCR eğitim verileri kesin olarak dışlandı.

## CI/CD ve Altyapı
- GitHub Actions CI workflow'u (`.github/workflows/ci.yml`) oluşturuldu. Node.js 22 ve 24 matrix desteği ile syntax check, `test:all` ve `eval` otomasyona bağlandı.
- MIT Lisansı, Güvenlik Politikası (`SECURITY.md`), Katkı Kılavuzu (`CONTRIBUTING.md`) ve Issue/PR şablonları eklendi.
