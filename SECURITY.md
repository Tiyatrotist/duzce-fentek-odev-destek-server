# Güvenlik Politikası (Security Policy)

Bu proje eğitim ortamlarında güvenli, yerel ve etik veri paylaşımını hedeflemektedir.

## Desteklenen Sürümler

| Sürüm | Destek Durumu |
| :--- | :--- |
| 1.0.x | :white_check_mark: Destekleniyor |
| < 1.0.0 | :x: Desteklenmiyor |

## Güvenlik İlkeleri ve Tasarım Sınırları

1. **Varsayılan Olarak Yerel (Loopback Default):** Sunucu varsayılan olarak `HOST=127.0.0.1` dinler. Ağ erişimi (`0.0.0.0`) bilinçli bir konfigürasyon tercihi olmalıdır.
2. **Karantina Odaklı Medya Deposu:** Yüklenen video ve medya dosyaları kamuya açık URL'lerle doğrudan servis edilmez. Tüm dosyalar öngörülemez anahtarlarla karantina deposunda (`VIDEO_STORE`) tutulur ve yalnızca yetkili denetimden geçtikten sonra Range akışıyla sunulur.
3. **AES-256-GCM Şifreli Sınav Havuzu:** Öğretmen soru ve sınav havuzu veritabanında düz metin saklanmaz; `SINAV_KEY` ile AES-256-GCM olarak şifrelenir. Anahtar bulunmadığında havuz 503 verir ve içerik incelemede bekler (fail-safe).
4. **Kişisel Veri ve Not İzolasyonu:** Notlar, E-Karne deneme kayıtları ve çalışma programları kesin sahiplik doğrulamasıyla (`WHERE yazar=?` / `sahip=?`) ayrıştırılır; yatay yetki aşımı (IDOR) engellenmiştir.
5. **Anti-Bot ve Hız Sınırları:** Okul gibi paylaşımlı NAT/IP ağlarında öğrencilerin topluca engellenmesini önlemek amacıyla hız sınırları kullanıcı + işlem türü bazında uygulanır; IP kontrolü yedek güvenlik tavanı olarak çalışır.
6. **Şifreleme ve Oturum Güvenliği:** Şifreler bcrypt ile tuzlanarak saklanır. Şifre değiştirildiğinde `sifre_surum` artırılarak eski tüm JWT biletleri derhal geçersiz kılınır.

## Güvenlik Açığı Bildirimi

Bir güvenlik açığı tespit ederseniz, lütfen bunu genel bir issue açarak **paylaşmayın**.

- GitHub repository'si üzerinden **Private Vulnerability Reporting** (Güvenlik Danışması) özelliğini kullanın.
- Veya maintainer ile doğrudan iletişime geçin: `179411334+Tiyatrotist@users.noreply.github.com`.

Bildiriminizde lütfen aşağıdaki detayları sağlayın:
- Açığın türü ve potansiyel etkisi
- Yeniden üretim adımları veya kavram kanıtı (PoC)
- Etkilenen uç noktalar veya bileşenler

Bildirimler 48 saat içinde değerlendirilir ve doğrulanan açıklar için en kısa sürede düzeltme yayınlanır.
