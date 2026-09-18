# Eylül Tiyatro — Masterplan

## Amaç

EylulTiyatro.com; Eylül Tiyatro topluluğunu, oyunlarını, oyuncularını ve etkinliklerini tanıtan; haber, duyuru ve tiyatro içeriklerinin admin panelden yönetilebildiği sade ve hızlı bir web sitesi olacaktır.

İlk hedef büyük ve karmaşık bir platform kurmak değil, kısa sürede yayına çıkabilecek sağlam bir temel oluşturmaktır.

---

## 1. İlk Sürümde Olacaklar

### Public site

- Ana sayfa
- Oyunlar
- Oyun detay sayfası
- Oyuncular
- Oyuncu detay sayfası
- Gösterimler
- Haberler
- Duyurular
- Bilgi Merkezi / Blog
- Hakkımızda
- İletişim

### Admin panel

- Oyun ekleme / düzenleme
- Oyuncu ekleme / düzenleme
- Oyuncuyu oyuna bağlama
- Gösterim ekleme
- Haber ekleme
- Duyuru ekleme
- Makale ekleme
- Görsel yükleme
- Yayınla / taslak işlemleri

---

## 2. Teknik Yapı

İlk sürüm mümkün olduğunca sade tutulacaktır.

```text
Next.js
TypeScript
Tailwind CSS
Prisma
PostgreSQL
```

Public site ve admin panel aynı Next.js projesi içinde olacaktır.

Ayrı backend, microservice veya ayrı admin uygulaması kurulmayacaktır.

---

## 3. Temel Veri Yapısı

### Oyun

```text
Play
- title
- slug
- description
- poster
- author
- director
- genre
- duration
- status
```

### Oyuncu

```text
Actor
- name
- slug
- photo
- shortBio
- biography
- active
```

### Oyuncu Kadrosu

```text
PlayCast
- playId
- actorId
- characterName
- sortOrder
```

Bir oyuncu birden fazla oyunda yer alabilir.

### Gösterim

```text
Performance
- playId
- venue
- date
- time
- ticketUrl
```

Oyun ile gösterim ayrı tutulacaktır.

### Haber

```text
News
- title
- slug
- summary
- content
- image
- publishedAt
- status
```

### Duyuru

```text
Announcement
- title
- content
- startDate
- endDate
- status
```

### Makale

```text
Article
- title
- slug
- summary
- content
- image
- category
- publishedAt
- status
```

---

## 4. SEO

İlk sürümde temel SEO eksiksiz yapılacaktır.

Her içerikte:

- temiz URL
- title
- description
- sosyal medya görseli
- canonical
- sitemap
- robots.txt

olacaktır.

Bilgi Merkezi SEO açısından sitenin önemli bölümlerinden biri olacaktır.

Başlangıç konuları:

- Tiyatro tarihi
- Türk tiyatrosu
- Dünya tiyatrosu
- Dünya klasikleri
- Oyun yazarları
- Oyunculuk
- Tiyatro terimleri

---

## 5. Admin Yetkilendirme

İlk aşamada karmaşık rol sistemi kurulmayacaktır.

Başlangıç için:

```text
ADMIN
EDITOR
```

yeterlidir.

Admin tüm sistemi yönetebilir.

Editor içerik ekleyip düzenleyebilir.

Gerekirse ileride daha ayrıntılı yetki sistemi eklenir.

---

## 6. Görseller

İlk sürümde:

- oyuncu fotoğrafı
- oyun afişi
- haber görseli
- makale görseli

yüklenebilmelidir.

Başlangıçta karmaşık medya kütüphanesi yapılmayacaktır.

Görseller optimize edilerek gösterilecektir.

---

## 7. Geliştirme Sırası

### P0 — Proje İskeleti

- Next.js
- TypeScript
- Tailwind
- Prisma
- PostgreSQL
- temel layout
- admin login

### P1 — Oyun ve Oyuncu

- oyun CRUD
- oyuncu CRUD
- oyuncu kadrosu ilişkisi
- public oyun sayfaları
- public oyuncu sayfaları

### P2 — Gösterimler

- gösterim ekleme
- yaklaşan gösterimler
- geçmiş gösterimler
- harici bilet linki

### P3 — İçerik

- haber
- duyuru
- makale
- Bilgi Merkezi

### P4 — Sonlandırma

- ana sayfa
- hakkımızda
- iletişim
- SEO
- mobil uyumluluk
- temel testler
- production yayını

---

## 8. İlk Yayın Kapsamı

İlk yayın için hedef:

```text
Ana sayfa
Oyunlar
Oyuncular
Gösterimler
Haberler
Duyurular
Bilgi Merkezi
Hakkımızda
İletişim
Admin panel
SEO
```

İlk sürümde yapılmayacaklar:

- online ödeme
- dahili bilet satış sistemi
- koltuk seçimi
- QR check-in
- üyelik sistemi
- gelişmiş rol/yetki sistemi
- gelişmiş medya yönetimi
- microservice
- ayrı backend
- gereksiz altyapı katmanları

---

## 9. Bilet Sistemi

İlk sürümde sadece gösterime harici bir bilet linki eklenebilir.

```text
Bilet Al
→ dış bilet satış sayfası
```

EylulTiyatro.com içinde bilet satışı ancak gerçekten ihtiyaç oluştuğunda ayrı faz olarak geliştirilecektir.

Mevcut veri modeli bunu sonradan eklemeye uygun tutulacaktır.

---

## 10. Temel Prensip

Bu projenin ana yaklaşımı:

> Önce çalışan ve içerik girilebilen siteyi hızlıca yayına çıkar. İhtiyaç oluşmadan karmaşıklık ekleme.

Başarı kriteri çok büyük bir teknik mimari kurmak değil; Eylül Tiyatro'nun güncel, hızlı, kolay yönetilen ve Google tarafından düzgün indekslenen bir web sitesine sahip olmasıdır.
