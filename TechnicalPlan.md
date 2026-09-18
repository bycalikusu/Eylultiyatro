# Eylül Tiyatro — Technical Plan

## Amaç

Bu belge, `Masterplan.md` içindeki kapsamın nasıl uygulanacağını tanımlar.

Hedef:
- hızlı geliştirme
- gereksiz mimari karmaşıklık oluşturmama
- içeriklerin admin panelden yönetilebilmesi
- mobil uyumlu ve SEO açısından düzgün bir public site
- ilk yayına mümkün olan en kısa ve kontrollü şekilde çıkmak

Teknik temel:

```text
Next.js
TypeScript
Tailwind CSS
MySQL
mysql2
```

Public site ve admin panel aynı Next.js uygulaması içinde olacaktır.

---

# 1. Genel Geçiş Kuralı

Her faz 10 maddelik kontrol listesi ile değerlendirilir.

Her madde:

```text
PASS = 1
FAIL = 0
```

Bir fazın geçme notu:

> **10 / 10**

9/10 dahi olsa faz tamamlanmış sayılmaz.

Bir faz tamamlanmadan sonraki faza geçilebilir ancak önceki faz:
- açık olarak `NOT DONE` işaretlenmeli
- eksik kriter yazılmalı
- production release öncesinde mutlaka 10/10 yapılmalıdır

Production yayını için tüm zorunlu fazlar 10/10 olmalıdır.

---

# 2. Kanıt Standardı

Bir kriter yalnızca "yapıldı" denilerek PASS sayılamaz.

Mümkün olan yerlerde şu kanıtlardan en az biri bulunmalıdır:

- çalışan ekran
- test sonucu
- build sonucu
- typecheck sonucu
- lint sonucu
- veritabanı kaydı
- HTTP response
- ekran görüntüsü
- manuel doğrulama notu
- CI çıktısı

Her faz sonunda kısa bir kabul kaydı tutulmalıdır:

```text
FAZ:
TARIH:
COMMIT:
SONUC: 10/10 PASS
NOTLAR:
```

---

# 3. Ortak Başarı Faktörleri

Tüm proje boyunca aşağıdaki ilkeler korunacaktır.

## 3.1 Sadelik

- gereksiz servis eklenmeyecek
- ayrı backend kurulmayacak
- microservice kullanılmayacak
- ORM zorunlu tutulmayacak
- ihtiyaç oluşmadan yeni altyapı eklenmeyecek

## 3.2 Kod Kalitesi

- TypeScript hatasız
- kritik kodlar tekrar etmeyecek
- SQL sorguları parametreli olacak
- server/client ayrımı doğru yapılacak
- dosya ve modül isimleri tutarlı olacak

## 3.3 Güvenlik

- admin sayfaları yetkisiz açılamayacak
- şifreler düz metin tutulmayacak
- SQL injection engellenecek
- form girdileri server tarafında doğrulanacak
- gizli bilgiler repoya yazılmayacak

## 3.4 Kullanılabilirlik

- admin içerik girişi kolay olacak
- public site mobilde düzgün çalışacak
- boş durumlar anlaşılır olacak
- hata mesajları kullanıcıyı yönlendirecek

## 3.5 SEO

- indexlenmesi gereken sayfalar server tarafından okunabilir olacak
- title ve description üretilecek
- slug yapısı temiz olacak
- sitemap ve robots.txt doğru çalışacak

---

# 4. P0 — Foundation

## Amaç

Projenin çalışır, güvenli ve geliştirilebilir temelini kurmak.

## Kapsam

- Next.js kurulumu
- TypeScript
- Tailwind CSS
- MySQL bağlantısı
- `mysql2`
- environment yapısı
- temel klasör düzeni
- public layout
- admin layout
- admin login
- session kontrolü
- temel hata yönetimi

## P0 Kabul Kriterleri — 10/10

### 1. Uygulama ayağa kalkıyor
`npm run dev` ile proje hatasız açılmalı.

### 2. Production build geçiyor
`npm run build` başarılı olmalı.

### 3. TypeScript temiz
Typecheck hata vermemeli.

### 4. MySQL bağlantısı çalışıyor
Uygulama MySQL'e bağlanabilmeli ve basit test sorgusu dönebilmeli.

### 5. Environment güvenli
`.env` repoda olmamalı, `.env.example` bulunmalı.

### 6. Admin login çalışıyor
Geçerli kullanıcı giriş yapabilmeli.

### 7. Yetkisiz admin erişimi engelleniyor
Login olmadan `/admin` açılamamalı.

### 8. Session/logout çalışıyor
Kullanıcı çıkış yaptığında admin oturumu kapanmalı.

### 9. Temel layout hazır
Public ve admin alanı birbirinden ayrılmış olmalı.

### 10. Hata durumları kontrol altında
DB bağlantı hatası veya geçersiz login uygulamayı kırmamalı.

## P0 Başarı Faktörleri

- proje tek komutla çalışmalı
- yeni geliştirici hızlıca ayağa kaldırabilmeli
- DB bilgileri kod içine gömülmemeli
- auth yalnızca frontend kontrolüne dayanmamalı

## P0 Geçiş

```text
10/10 = PASS
0-9/10 = FAIL
```

---

# 5. P1 — Oyunlar / Oyuncular / Kadro

## Amaç

Sitenin ana içerik omurgasını oluşturmak.

## Kapsam

- oyun CRUD
- oyuncu CRUD
- oyun-oyuncu ilişkisi
- rol adı
- sıralama
- oyun afişi
- oyuncu fotoğrafı
- slug
- yayın durumu
- public oyun ve oyuncu sayfaları

## Minimum Veri Yapısı

### plays

```text
id
title
slug
description
poster
author
director
genre
duration
status
created_at
updated_at
```

### actors

```text
id
name
slug
photo
short_bio
biography
active
created_at
updated_at
```

### play_cast

```text
id
play_id
actor_id
character_name
sort_order
```

## P1 Kabul Kriterleri — 10/10

### 1. Oyun eklenebiliyor
Admin yeni oyun oluşturabilmeli.

### 2. Oyun düzenlenebiliyor
Mevcut oyun değiştirilebilmeli.

### 3. Oyuncu eklenebiliyor
Admin fotoğraflı oyuncu profili oluşturabilmeli.

### 4. Oyuncu düzenlenebiliyor
Mevcut oyuncu bilgileri güncellenebilmeli.

### 5. Kadro ilişkisi çalışıyor
Bir oyuncu bir oyuna rol adı ile bağlanabilmeli.

### 6. Çoklu ilişki çalışıyor
Bir oyuncu birden fazla oyunda bulunabilmeli.

### 7. Oyun detay sayfası çalışıyor
Public sayfada oyun bilgileri ve oyuncu kadrosu görünmeli.

### 8. Oyuncu detay sayfası çalışıyor
Oyuncunun oynadığı oyunlar görünmeli.

### 9. Slug benzersiz ve temiz
Aynı slug ikinci kez oluşturulamamalı.

### 10. Yayın kontrolü çalışıyor
Taslak içerikler public tarafta görünmemeli.

## P1 Başarı Faktörleri

- oyun ve gösterim kavramları karıştırılmamalı
- oyuncu bilgileri tekrar tekrar oyun kaydına yazılmamalı
- kadro ilişkisi ayrı tabloda tutulmalı
- CRUD ekranları hızlı içerik girişine uygun olmalı

## P1 Geçiş

```text
10/10 = PASS
0-9/10 = FAIL
```

---

# 6. P2 — Gösterimler

## Amaç

Bir oyunun farklı tarih ve mekanlardaki gösterimlerini yönetmek.

## Kapsam

- gösterim ekleme
- gösterim düzenleme
- oyun seçme
- mekan bilgisi
- tarih
- saat
- harici bilet linki
- yaklaşan gösterimler
- geçmiş gösterimler

## Minimum Veri Yapısı

### performances

```text
id
play_id
venue
date
time
ticket_url
status
created_at
updated_at
```

İlk sürümde ayrı `venues` tablosu zorunlu değildir.
İhtiyaç oluşursa daha sonra ayrıştırılabilir.

## P2 Kabul Kriterleri — 10/10

### 1. Gösterim oluşturulabiliyor
Admin bir oyuna gösterim ekleyebilmeli.

### 2. Gösterim düzenlenebiliyor
Tarih, saat ve mekan değiştirilebilmeli.

### 3. Oyun ilişkisi doğru
Gösterim mutlaka bir oyuna bağlı olmalı.

### 4. Yaklaşan gösterimler listeleniyor
Bugünden sonraki aktif gösterimler doğru sıralanmalı.

### 5. Geçmiş gösterimler ayrılıyor
Geçmiş tarihli etkinlikler ayrı gösterilebilmeli.

### 6. Tarih/saat doğrulanıyor
Geçersiz tarih veya saat kabul edilmemeli.

### 7. Harici bilet linki çalışıyor
Bilet linki varsa public sayfada "Bilet Al" aksiyonu görünmeli.

### 8. Link yoksa hata oluşmuyor
Bilet linki olmayan gösterim düzgün görüntülenmeli.

### 9. Public oyun sayfasında gösterimler var
Oyuna ait yaklaşan gösterimler oyun detayında görünmeli.

### 10. Silinen/pasif gösterim görünmüyor
Pasif veya kaldırılmış kayıt public tarafta listelenmemeli.

## P2 Başarı Faktörleri

- bilet satışı site içine alınmamalı
- yalnızca ihtiyaç kadar veri tutulmalı
- tarih sıralaması güvenilir olmalı
- mobil ekranda gösterim bilgisi kolay okunmalı

## P2 Geçiş

```text
10/10 = PASS
0-9/10 = FAIL
```

---

# 7. P3 — Haber / Duyuru / Bilgi Merkezi

## Amaç

Sitenin güncel ve SEO odaklı içerik tarafını tamamlamak.

## Kapsam

- haber CRUD
- duyuru CRUD
- makale CRUD
- kategori
- kapak görseli
- slug
- taslak/yayın
- yayın tarihi
- basit zengin metin editörü

## P3 Kabul Kriterleri — 10/10

### 1. Haber oluşturulabiliyor
Başlık, özet, içerik ve görsel ile haber eklenebilmeli.

### 2. Haber public tarafta açılıyor
Yayınlanan haber detay sayfası düzgün çalışmalı.

### 3. Duyuru oluşturulabiliyor
Admin duyuru ekleyebilmeli.

### 4. Duyuru tarih kontrolü çalışıyor
Başlangıç/bitiş tarihine göre aktif duyurular gösterilebilmeli.

### 5. Makale oluşturulabiliyor
Bilgi Merkezi için makale girilebilmeli.

### 6. Makale kategorisi çalışıyor
Makale bir kategoriye bağlanabilmeli.

### 7. Taslak içerik görünmüyor
Yayınlanmamış haber/makale public tarafta açılmamalı.

### 8. Slug ve URL düzgün
Haber ve makale URL'leri temiz ve benzersiz olmalı.

### 9. İçerik editörü güvenli
HTML/script enjeksiyonuna karşı içerik kontrol edilmeli.

### 10. Listeleme düzgün
Haberler, duyurular ve makaleler tarih sırasına göre düzgün listelenmeli.

## P3 Başarı Faktörleri

- haber ve duyuru ayrı tutulmalı
- makale yapısı SEO içerik üretimine uygun olmalı
- editör karmaşık olmamalı
- içerik girişi birkaç dakikada yapılabilmeli

## P3 Geçiş

```text
10/10 = PASS
0-9/10 = FAIL
```

---

# 8. P4 — Public Site / SEO / Yayın

## Amaç

Tüm sistemi kullanıcıya hazır hale getirip production'a çıkmak.

## Kapsam

- ana sayfa
- oyunlar
- oyuncular
- gösterimler
- haberler
- duyurular
- Bilgi Merkezi
- hakkımızda
- iletişim
- mobil uyumluluk
- temel SEO
- sitemap
- robots.txt
- production deploy

## P4 Kabul Kriterleri — 10/10

### 1. Ana sayfa tamam
En az şu bölümler bulunmalı:
- güncel/öne çıkan oyun
- yaklaşan gösterimler
- son haberler
- Bilgi Merkezi

### 2. Ana navigation çalışıyor
Tüm ana sayfalara hatasız ulaşılabilmeli.

### 3. Mobil görünüm düzgün
Temel sayfalar telefon ekranında taşmamalı ve kullanılabilir olmalı.

### 4. 404/hata sayfaları var
Geçersiz URL uygulamayı kırmamalı.

### 5. SEO metadata var
Ana içerik sayfalarında title ve description bulunmalı.

### 6. Sitemap çalışıyor
`/sitemap.xml` geçerli ve erişilebilir olmalı.

### 7. Robots çalışıyor
`/robots.txt` doğru şekilde dönmeli.

### 8. İletişim çalışıyor
İletişim bilgileri veya form erişilebilir ve kullanılabilir olmalı.

### 9. Production build/deploy başarılı
Canlı ortam açılmalı ve temel sayfalar HTTP 200 dönmeli.

### 10. Smoke test tamam
Production üzerinde ana akışlar manuel olarak doğrulanmalı:
- oyun görüntüle
- oyuncu görüntüle
- gösterim görüntüle
- haber görüntüle
- makale görüntüle
- admin login

## P4 Başarı Faktörleri

- site hızlı açılmalı
- mobil kullanım sorunsuz olmalı
- içerik yönetimi için kod değişikliği gerekmemeli
- Google'ın okuyabileceği server-rendered içerik bulunmalı
- production'da kritik console/server hatası olmamalı

## P4 Geçiş

```text
10/10 = PASS
0-9/10 = FAIL
```

---

# 9. Production Release Gate

Production release için aşağıdaki tablo tamamen PASS olmalıdır.

| Faz | Zorunlu Not |
|---|---:|
| P0 Foundation | 10/10 |
| P1 Oyun/Oyuncu | 10/10 |
| P2 Gösterimler | 10/10 |
| P3 İçerik | 10/10 |
| P4 Public/SEO/Yayın | 10/10 |

Toplam:

```text
50 / 50
```

gereklidir.

Herhangi bir faz 9/10 veya altında ise:

> **RELEASE BLOCKED**

---

# 10. Kritik Test Senaryoları

Production öncesinde minimum aşağıdaki senaryolar çalıştırılmalıdır.

## Admin

1. doğru şifre ile login
2. yanlış şifre ile login reddi
3. oyun oluştur
4. oyuncu oluştur
5. oyuncuyu oyuna bağla
6. gösterim ekle
7. haber yayınla
8. duyuru yayınla
9. makale yayınla
10. logout

## Public

1. ana sayfa açılıyor
2. oyun listesi açılıyor
3. oyun detay ve kadro görünüyor
4. oyuncu detay ve oyunları görünüyor
5. yaklaşan gösterimler görünüyor
6. bilet linki çalışıyor
7. haber detayı açılıyor
8. Bilgi Merkezi makalesi açılıyor
9. mobil navigation çalışıyor
10. taslak içerik URL ile açılamıyor

Bu iki grup da 10/10 geçmelidir.

---

# 11. Minimum Güvenlik Kontrolü

Production öncesinde:

- admin şifresi hash'li
- session cookie güvenli
- SQL sorguları parametreli
- login rate limit veya temel brute-force koruması
- form validation server-side
- dosya yükleme tipi kontrolü
- `.env` repoda yok
- admin route server-side korumalı
- taslak içerik erişimi engelli
- hata mesajları secret/SQL bilgisi sızdırmıyor

Sonuç:

```text
10/10 PASS
```

olmalıdır.

---

# 12. Minimum SEO Kontrolü

Production öncesinde:

- ana sayfa title
- ana sayfa description
- oyun title/description
- oyuncu title/description
- haber title/description
- makale title/description
- canonical URL
- sitemap
- robots.txt
- indexlenebilir içerik server response içinde mevcut

Sonuç:

```text
10/10 PASS
```

olmalıdır.

---

# 13. Minimum Mobil Kontrolü

Aşağıdaki sayfalar telefon görünümünde kontrol edilmelidir:

1. ana sayfa
2. oyun listesi
3. oyun detay
4. oyuncu listesi
5. oyuncu detay
6. gösterimler
7. haberler
8. haber detay
9. Bilgi Merkezi
10. admin temel içerik ekranı

Her biri kullanılabilir olmalıdır.

Sonuç:

```text
10/10 PASS
```

olmalıdır.

---

# 14. Definition of Done

Bir geliştirme işi DONE sayılmadan önce:

- kod tamam
- işlev manuel doğrulandı
- hata durumu kontrol edildi
- server validation var
- ilgili DB sorgusu güvenli
- mobil görünüm kontrol edildi
- mevcut özellikleri bozmadı
- build geçiyor
- typecheck geçiyor
- ilgili faz kabul kriterini karşılıyor

Eksik madde varsa iş DONE değildir.

---

# 15. Öncelik Sırası

Geliştirme sırası:

```text
P0
↓
P1
↓
P2
↓
P3
↓
P4
↓
PRODUCTION
```

Öncelik:

1. çalışan veri modeli
2. çalışan admin
3. içerik girişi
4. public sayfalar
5. SEO
6. görsel iyileştirmeler

Tasarım detayları işlevsel sistemi geciktirmemelidir.

---

# 16. Kapsam Dışı

İlk sürümde yapılmayacaktır:

- site içi ödeme
- QR bilet
- koltuk seçimi
- üyelik sistemi
- gelişmiş rol matrisi
- microservice
- ayrı API projesi
- gelişmiş medya DAM sistemi
- yüksek trafik için özel cache cluster
- Kubernetes
- Redis zorunluluğu
- Elasticsearch

Bunlar ancak gerçek ihtiyaç oluşursa değerlendirilir.

---

# 17. Proje Başarı Tanımı

İlk sürüm başarılı sayılırsa:

- topluluk kendini düzgün tanıtabiliyor
- oyunlar admin panelden girilebiliyor
- oyuncular ve rolleri yönetilebiliyor
- gösterimler yayınlanabiliyor
- haber/duyuru girilebiliyor
- Bilgi Merkezi içerikleri yayınlanabiliyor
- site mobilde düzgün çalışıyor
- Google siteyi tarayabiliyor
- içerik güncellemek için geliştiriciye ihtiyaç duyulmuyor
- production kritik hatasız çalışıyor

Teknik başarı ölçüsü büyük bir altyapı kurmak değil:

> **küçük, anlaşılır, hızlı geliştirilen ve sorunsuz çalışan bir sistem kurmaktır.**
