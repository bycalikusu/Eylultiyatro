# Eylül Tiyatro — Master Plan

**Proje:** EylulTiyatro.com  
**Repo:** `https://github.com/bycalikusu/Eylultiyatro`  
**Belge:** `Masterplan.md`  
**Durum:** Başlangıç teknik planı  
**Amaç:** Eylül Tiyatro topluluğunun dijital merkezi olacak; oyunları, oyuncuları, gösterimleri, haberleri, duyuruları ve tiyatro bilgi merkezini yönetecek; ileride bilet satışına genişleyebilecek bir platform oluşturmak.

---

## 1. Proje Vizyonu

EylulTiyatro.com yalnızca bir tanıtım sitesi olmayacaktır.

Platformun hedefi:

- Eylül Tiyatro topluluğunu ve kimliğini tanıtmak
- Oynanan ve hazırlanmakta olan oyunları yayınlamak
- Oyuncuları fotoğraflı profiller ile tanıtmak
- Gösterimleri ve etkinlikleri yayınlamak
- Haber ve duyuruları yönetmek
- Tiyatro tarihi, dünya klasikleri, Türk tiyatrosu, oyunculuk ve sahne sanatları üzerine SEO odaklı bir Bilgi Merkezi oluşturmak
- Fotoğraf ve medya arşivi oluşturmak
- Tüm içerikleri admin panel üzerinden yönetmek
- İleride bilet satışı, QR bilet, ödeme, salon ve koltuk yönetimi eklemek
- Uzun vadede Eylül Tiyatro'nun dijital arşivi haline gelmek

---

# 2. Temel Mimari Kararı

## 2.1 Mimari yaklaşım

İlk sürüm için:

> **Modüler Monolith**

kullanılacaktır.

Microservice mimarisi ilk aşamada kullanılmayacaktır.

Sebep:

- Gereksiz operasyon yükünü önler
- Daha hızlı geliştirme sağlar
- Deployment basitleşir
- Test kolaylaşır
- Domain modülleri yine birbirinden ayrılabilir
- Gerektiğinde belirli servisler ileride ayrıştırılabilir

---

# 3. Teknoloji Yığını

## Frontend

- Next.js
- React
- TypeScript
- Tailwind CSS

## Backend

İlk sürümde Next.js server-side altyapısı kullanılacaktır.

Katmanlar:

```text
UI
↓
Route / Server Action
↓
Service Layer
↓
Repository / Prisma
↓
PostgreSQL
```

İleride ihtiyaç oluşursa backend bağımsız NestJS servisine ayrılabilir.

## Database

- PostgreSQL

## ORM

- Prisma

## Authentication

- Güvenli session tabanlı authentication
- HttpOnly cookie
- RBAC yetkilendirme

## Deployment

- Docker
- Nginx / reverse proxy
- CI/CD
- Production server
- Object storage destekli medya sistemi

---

# 4. Uygulama Alanları

Sistem iki ana alandan oluşacaktır.

## 4.1 Public Site

```text
/
├── oyunlar
├── oyuncular
├── gosterimler
├── haberler
├── duyurular
├── bilgi-merkezi
├── hakkimizda
├── galeri
└── iletisim
```

## 4.2 Admin Panel

```text
/admin
├── dashboard
├── oyunlar
├── oyuncular
├── gosterimler
├── salonlar
├── haberler
├── duyurular
├── makaleler
├── kategoriler
├── etiketler
├── medya
├── sayfalar
├── kullanicilar
└── ayarlar
```

---

# 5. Domain Modülleri

Sistem aşağıdaki modüllere ayrılacaktır:

1. Authentication
2. Authorization / RBAC
3. Users
4. Plays
5. Actors
6. Cast
7. Performances
8. Venues
9. News
10. Announcements
11. Articles
12. Categories
13. Tags
14. Media
15. Pages
16. SEO
17. Site Settings
18. Audit Logs
19. Search
20. Future Ticketing

---

# 6. Ana Veri Modeli

## 6.1 Plays

Bir tiyatro eserini temsil eder.

```text
plays
-----
id
title
slug
short_description
description
poster_id

author
translator
director

genre
duration_minutes
age_rating

status
premiere_date

seo_title
seo_description
canonical_url

created_at
updated_at
published_at
```

### Status

```text
draft
published
archived
```

---

# 7. Actors

```text
actors
------
id
name
slug

profile_image_id

short_bio
biography

birth_year
instagram_url
youtube_url
website_url

active

seo_title
seo_description

created_at
updated_at
```

Kişisel veri gerektirmeyen alanlar tercih edilmelidir.

---

# 8. Play Cast

Oyuncu ile oyun arasındaki ilişki.

```text
play_cast
---------
id
play_id
actor_id

character_name
cast_type
sort_order

created_at
updated_at
```

Bu yapı sayesinde:

```text
Oyuncu → oynadığı oyunlar
Oyun → oyuncu kadrosu
```

otomatik oluşturulur.

---

# 9. Performances

Bir oyunun belirli tarih ve yerdeki gösterimidir.

```text
performances
------------
id
play_id
venue_id

performance_date
start_time
end_time

status

ticket_status
external_ticket_url

capacity
notes

created_at
updated_at
```

### Önemli ayrım

```text
"Lüküs Hayat"
= Play

18 Ekim 2026 / 20:30 / X Sahnesi
= Performance
```

Bu ayrım ileride bilet sisteminin temelidir.

---

# 10. Venues

```text
venues
------
id
name
slug

city
district
address

latitude
longitude

website_url
phone

created_at
updated_at
```

---

# 11. News

```text
news
----
id
title
slug
summary
content
cover_image_id

status
published_at

seo_title
seo_description

created_at
updated_at
```

İlişkiler:

```text
news_play
news_actor
```

Bir haber bir veya birden fazla oyun/oyuncu ile ilişkilendirilebilir.

---

# 12. Announcements

Haberlerden ayrı tutulacaktır.

```text
announcements
-------------
id
title
slug
summary
content

priority

start_date
end_date

status

created_at
updated_at
```

Örnek:

- Yeni oyun duyurusu
- Oyuncu seçmeleri
- Etkinlik çağrısı
- Gösterim değişikliği
- Topluluk duyurusu

---

# 13. Bilgi Merkezi / Articles

Bilgi Merkezi SEO stratejisinin ana omurgasıdır.

```text
articles
--------
id
title
slug

excerpt
content

category_id
author_user_id
cover_image_id

status

published_at
updated_at

seo_title
seo_description
canonical_url
```

---

# 14. Article Categories

Başlangıç kategorileri:

```text
Tiyatro Tarihi
Türk Tiyatrosu
Dünya Tiyatrosu
Dünya Klasikleri
Oyun Yazarları
Türk Oyun Yazarları
Oyunculuk
Sahne Teknikleri
Tiyatro Terimleri
```

Kategori yapısı ileride hiyerarşik hale getirilebilir.

---

# 15. Article Tags

```text
article_tags
------------
article_id
tag_id
```

Örnek tag'ler:

```text
Anton Çehov
Shakespeare
Stanislavski
Absürt Tiyatro
Türk Tiyatrosu
Geleneksel Tiyatro
```

---

# 16. Related Content

İçerikler birbirine bağlanmalıdır.

Örnek:

```text
Anton Çehov Kimdir?
↓
Çehov'un Tiyatro Anlayışı
↓
Martı
↓
Vanya Dayı
```

İlişki modeli:

```text
related_content
---------------
source_type
source_id
target_type
target_id
sort_order
```

İlk sürümde daha basit yapı da kullanılabilir.

---

# 17. Medya Sistemi

## 17.1 Amaç

- Oyun afişleri
- Oyuncu fotoğrafları
- Sahne fotoğrafları
- Prova fotoğrafları
- Haber görselleri
- Makale görselleri
- Galeri

tek merkezden yönetilecektir.

## 17.2 Media modeli

```text
media
-----
id
filename
original_name

mime_type
size

width
height

alt_text
caption

storage_provider
storage_path

created_at
updated_at
```

---

# 18. Görsel İşleme

Yüklenen görseller için otomatik varyant üretimi hedeflenmelidir.

Önerilen boyutlar:

```text
original
1920
1200
800
400
```

Destek:

```text
AVIF
WebP
fallback
```

Avantaj:

- daha hızlı sayfalar
- daha düşük trafik
- daha iyi Core Web Vitals
- daha iyi SEO

---

# 19. SEO

SEO sonradan eklenen bir özellik olmayacaktır.

Her yayınlanabilir içerikte aşağıdaki alanlar bulunacaktır:

```text
slug
seo_title
seo_description
canonical_url
og_image
robots_index
robots_follow
```

---

# 20. URL Standardı

Önerilen yapı:

```text
/

/oyunlar
/oyunlar/lukus-hayat

/oyuncular
/oyuncular/ad-soyad

/gosterimler

/haberler
/haberler/yeni-oyunumuz

/duyurular
/duyurular/oyuncu-secmeleri

/bilgi-merkezi
/bilgi-merkezi/tiyatro-tarihi
/bilgi-merkezi/turk-tiyatrosu
/bilgi-merkezi/anton-cehov

/hakkimizda
/iletisim
```

Slug alanları ASCII normalize edilmelidir.

Örnek:

```text
Türk Tiyatrosu
→ turk-tiyatrosu
```

---

# 21. Structured Data

Schema.org structured data kullanılacaktır.

## Organization

```text
Organization
PerformingGroup
```

## Actor

```text
Person
```

## Article

```text
Article
BlogPosting
```

## Performance

```text
Event
TheaterEvent
```

## Breadcrumb

```text
BreadcrumbList
```

Structured data server-side üretilecektir.

---

# 22. Sitemap

Dinamik sitemap üretilecektir.

Kapsam:

```text
static pages
plays
actors
performances
news
articles
categories
```

Dosyalar:

```text
/sitemap.xml
/robots.txt
```

İçerik büyüdüğünde sitemap index kullanılabilir.

---

# 23. Admin Panel

Admin panel yalnızca CRUD ekranı olmayacaktır.

Ana dashboard:

```text
Aktif oyunlar
Yaklaşan gösterimler
Taslak içerikler
Son haberler
Son duyurular
Son eklenen oyuncular
Son medya yüklemeleri
```

---

# 24. İçerik Editörü

Düz textarea kullanılmamalıdır.

Tercih:

- Tiptap
- Editor.js
- Lexical

Desteklenecek bloklar:

```text
paragraph
heading
image
quote
list
link
video embed
table
separator
```

İçerik formatı kontrollü ve sanitize edilmiş olmalıdır.

---

# 25. Kullanıcı Rolleri

Başlangıç rolleri:

```text
SUPER_ADMIN
ADMIN
EDITOR
AUTHOR
```

## SUPER_ADMIN

- tüm sistem
- kullanıcı yönetimi
- sistem ayarları
- yetki yönetimi

## ADMIN

- içerik yönetimi
- medya
- oyuncu
- oyun
- gösterim

## EDITOR

- içerik oluşturma
- içerik düzenleme
- içerik yayınlama

## AUTHOR

- kendi makalelerini oluşturma/düzenleme
- doğrudan yayınlama yetkisi opsiyonel

---

# 26. RBAC

Yetkiler rol adına hard-code edilmemelidir.

Örnek permission'lar:

```text
play.read
play.create
play.update
play.publish
play.delete

actor.read
actor.create
actor.update

article.create
article.update
article.publish

media.upload

user.manage
settings.manage
```

---

# 27. Audit Log

Yönetim işlemleri kayıt altına alınmalıdır.

```text
audit_logs
----------
id
user_id

action
entity_type
entity_id

before_json
after_json

ip_address
user_agent

created_at
```

Örnek:

```text
18.09.2026
Admin
"Martı" oyununu güncelledi.
```

---

# 28. Authentication Güvenliği

Minimum gereksinimler:

- HttpOnly cookie
- Secure cookie
- SameSite
- CSRF koruması
- Rate limiting
- Login throttling
- Password hashing
- Session invalidation
- Güvenli password reset
- XSS sanitation
- Input validation
- Server-side authorization
- Admin route protection

2FA sonraki fazda eklenebilir.

---

# 29. Validation

Tüm inputlar server tarafında doğrulanacaktır.

Öneri:

```text
Zod
```

Frontend doğrulama yalnızca UX içindir.

Gerçek güvenlik server-side validation ile sağlanmalıdır.

---

# 30. Search

İlk sürüm:

```text
PostgreSQL Full Text Search
```

Arama kapsamı:

```text
oyun
oyuncu
haber
makale
```

Büyüme durumunda:

```text
Meilisearch
```

veya benzeri servis kullanılabilir.

---

# 31. Cache Stratejisi

Public içerikler mümkün olduğunca cache edilecektir.

Önerilen yaklaşım:

```text
SSG
ISR
Server cache
```

İçerik yayınlandığında ilgili cache invalidate edilmelidir.

Örnek:

```text
Admin Publish
↓
revalidate
↓
public page refresh
```

---

# 32. Rendering Stratejisi

## Statik içerik

```text
Hakkımızda
Bilgi Merkezi
Oyun detayları
Oyuncu detayları
```

SSG / ISR kullanılabilir.

## Dinamik içerik

```text
Admin
Authentication
Yaklaşan gösterimler
```

SSR veya server rendering kullanılabilir.

---

# 33. Ana Sayfa

Ana sayfa aşağıdaki bölümleri desteklemelidir:

1. Hero
2. Yaklaşan gösterimler
3. Öne çıkan oyun
4. Güncel oyunlar
5. Son haberler
6. Duyurular
7. Oyuncular
8. Bilgi Merkezi
9. Fotoğraf / galeri
10. Topluluk hakkında kısa bölüm
11. İletişim / sosyal medya

Bölüm sırası admin panelden değiştirilebilir hale getirilebilir ancak V1 için zorunlu değildir.

---

# 34. Hakkımızda

İçerik alanları:

```text
Topluluğun hikâyesi
Misyon
Yaklaşım
Tiyatro anlayışı
Ekibimiz
Tarihçe
```

---

# 35. İletişim

Form:

```text
Ad Soyad
E-posta
Telefon (opsiyonel)
Konu
Mesaj
KVKK / onay
```

Spam önlemleri:

- honeypot
- rate limit
- CAPTCHA yalnızca gerekirse

---

# 36. Galeri

V1 sonrası veya V1 sonuna doğru eklenebilir.

```text
gallery
gallery_items
```

Galeri:

- oyun
- prova
- sahne arkası
- etkinlik

ile ilişkilendirilebilir.

---

# 37. Repository Yapısı

Başlangıçta tek uygulama tercih edilir.

```text
Eylultiyatro/
│
├── app/
│   ├── (public)/
│   ├── admin/
│   └── api/
│
├── components/
│
├── modules/
│   ├── auth/
│   ├── users/
│   ├── plays/
│   ├── actors/
│   ├── performances/
│   ├── news/
│   ├── announcements/
│   ├── articles/
│   ├── media/
│   └── seo/
│
├── lib/
│
├── prisma/
│   ├── schema.prisma
│   └── migrations/
│
├── public/
│
├── tests/
│
├── docs/
│
├── docker/
│
├── .env.example
├── docker-compose.yml
└── README.md
```

Monorepo ilk sürüm için zorunlu değildir.

---

# 38. Environment

En az üç ortam:

```text
local
staging
production
```

Öneri:

```text
eylultiyatro.com
staging.eylultiyatro.com
```

Production değişiklikleri staging üzerinde doğrulanmadan yayınlanmamalıdır.

---

# 39. Environment Variables

`.env` commit edilmeyecektir.

`.env.example` tutulacaktır.

Örnek:

```text
DATABASE_URL=

AUTH_SECRET=

APP_URL=

MEDIA_PROVIDER=
MEDIA_BUCKET=
MEDIA_ACCESS_KEY=
MEDIA_SECRET_KEY=

SMTP_HOST=
SMTP_PORT=
SMTP_USER=
SMTP_PASSWORD=
```

---

# 40. CI

Her PR için minimum:

```text
install
lint
typecheck
test
build
```

Başarısız CI ile merge yapılmamalıdır.

---

# 41. Test Stratejisi

## Unit Test

- service layer
- validation
- authorization
- utility

## Integration Test

- database
- repository
- authentication
- content publishing

## E2E

Kritik akışlar:

```text
Admin login
Oyun oluştur
Oyuncu oluştur
Kadroyu bağla
Gösterim oluştur
Makale yayınla
Public sayfada görüntüle
```

---

# 42. Logging

Production logları yapılandırılmış olmalıdır.

Alanlar:

```text
timestamp
level
request_id
route
user_id
message
error
```

Secret ve password loglanmamalıdır.

---

# 43. Monitoring

İleride:

- uptime monitoring
- error tracking
- performance monitoring

eklenmelidir.

Temel production sürümünde en az uptime kontrolü bulunmalıdır.

---

# 44. Backup

## Database

- günlük backup
- 7 günlük kısa dönem
- 30 günlük rotasyon

## Media

- object storage versioning veya yedek
- periyodik doğrulama

Backup alınması tek başına yeterli değildir.

Restore testi yapılmalıdır.

---

# 45. Bilet Sistemi — Gelecek Mimari

Bilet sistemi ilk sürümde aktif olmayacaktır ancak mevcut veri modeli buna uyumlu olacaktır.

Akış:

```text
Play
↓
Performance
↓
Ticket Type
↓
Order
↓
Payment
↓
Ticket
↓
QR Validation
```

---

# 46. Future Ticket Tables

```text
ticket_types
------------
id
performance_id
name
price
capacity
sales_start
sales_end

orders
------
id
customer_name
customer_email
status
total_amount

order_items
-----------
order_id
ticket_type_id
quantity
unit_price

payments
--------
order_id
provider
provider_reference
status
amount

tickets
-------
id
order_item_id
ticket_code
qr_token
checked_in_at
```

---

# 47. Koltuk Sistemi

İlk bilet sürümünde numarasız bilet düşünülebilir.

Koltuk seçimi gerekiyorsa ayrıca:

```text
venues
halls
seat_maps
seats
performance_seats
```

eklenebilir.

Bu nedenle ilk günden koltuk sistemi kurulmayacaktır.

---

# 48. QR Check-in

İleride:

```text
/admin/check-in
```

ekranı üzerinden telefon kamerasıyla QR okutulabilir.

Kurallar:

- tek kullanımlık check-in
- tekrar okutma uyarısı
- offline fallback opsiyonel
- işlem audit log'a yazılmalı

---

# 49. SEO İçerik Stratejisi

Bilgi Merkezi rastgele blog yazılarından oluşmamalıdır.

Topic cluster mantığı kullanılmalıdır.

Örnek cluster:

```text
Türk Tiyatrosu
├── Geleneksel Türk Tiyatrosu
├── Karagöz ve Hacivat
├── Orta Oyunu
├── Tanzimat Dönemi Tiyatrosu
├── Cumhuriyet Dönemi Tiyatrosu
└── Modern Türk Tiyatrosu
```

Başka örnek:

```text
Anton Çehov
├── Anton Çehov Kimdir?
├── Çehov'un Tiyatro Anlayışı
├── Martı
├── Vanya Dayı
├── Üç Kız Kardeş
└── Vişne Bahçesi
```

---

# 50. SEO Kalite Kuralı

SEO amacıyla düşük kaliteli, birbirini tekrar eden içerik üretilmemelidir.

Her makale:

- gerçek bilgi değeri taşımalı
- kaynaklandırılabilir olmalı
- konuya yeterli derinlikte yaklaşmalı
- başka içeriklere mantıklı internal link vermeli
- yapay anahtar kelime doldurma içermemeli

---

# 51. Analytics

İlk production sürümünde analytics altyapısı kurulmalıdır.

Takip edilecek ana metrikler:

```text
page views
organic traffic
article entrances
play page visits
performance click
contact conversion
ticket link clicks
```

Cookie/consent gereklilikleri kullanılan araca göre ayrıca değerlendirilmelidir.

---

# 52. Accessibility

Minimum hedef:

```text
WCAG 2.1 AA yaklaşımı
```

Önemli maddeler:

- semantic HTML
- keyboard navigation
- alt text
- focus states
- doğru heading sırası
- kontrast
- form label'ları

---

# 53. Performance Hedefleri

Public site için hedef:

```text
LCP < 2.5s
CLS < 0.1
INP < 200ms
```

Mobil performans öncelikli olacaktır.

---

# 54. Tasarım İlkeleri

Site:

- tiyatro kimliğini taşımalı
- görsel ağırlıklı olmalı
- karanlık sahne estetiğine izin verebilir
- ancak okunabilirliği bozmamalı
- afişleri ve oyuncuları öne çıkarmalı
- mobil kullanımda güçlü olmalı

Tasarım sistemi oluşturulacaktır.

---

# 55. Design System

Temel bileşenler:

```text
Button
Input
Textarea
Select
Modal
Drawer
Card
Badge
Tabs
Table
Pagination
Toast
Dialog
Image
Breadcrumb
SEOHead
EmptyState
ConfirmDialog
```

---

# 56. Admin UX İlkeleri

Admin panel kullanım kolaylığı önceliklidir.

Kurallar:

- hızlı içerik girişi
- taslak kaydetme
- otomatik slug
- medya seçici
- preview
- publish/unpublish
- duplicate
- filtreleme
- arama
- pagination
- toplu işlem gerektiğinde sonradan ekleme

---

# 57. Publish Workflow

V1:

```text
draft
published
archived
```

İleride:

```text
draft
review
approved
scheduled
published
archived
```

eklenebilir.

---

# 58. Soft Delete

Ana içeriklerde doğrudan fiziksel silme yerine soft delete tercih edilebilir.

```text
deleted_at
```

Admin kullanıcı yanlışlıkla içerik sildiğinde kurtarma mümkün olur.

---

# 59. Slug Yönetimi

Slug değişirse eski URL kırılmamalıdır.

```text
redirects
---------
from_path
to_path
status_code
```

Öneri:

```text
301
```

---

# 60. Site Settings

```text
site_settings
-------------
site_name
site_description
logo
favicon

email
phone

instagram
youtube
facebook

default_seo_title
default_seo_description
default_og_image
```

---

# 61. Faz Planı

## P0 — Foundation

Amaç: Projenin güvenli ve sürdürülebilir iskeletini kurmak.

İşler:

- Next.js kurulumu
- TypeScript strict
- Tailwind
- Prisma
- PostgreSQL
- Docker
- `.env.example`
- lint
- format
- CI
- base layout
- admin shell
- auth
- RBAC
- audit log temeli

### P0 kabul kriterleri

- local ortam tek komutla kalkmalı
- DB migration çalışmalı
- login çalışmalı
- `/admin` korumalı olmalı
- CI yeşil olmalı
- production secret repo içinde bulunmamalı

---

# 62. P1 — Plays / Actors / Cast

İşler:

- oyun CRUD
- oyuncu CRUD
- cast ilişkisi
- poster
- oyuncu fotoğrafı
- slug
- SEO alanları
- draft/publish

### Kabul kriterleri

- admin oyun ekleyebilmeli
- admin oyuncu ekleyebilmeli
- oyuncu oyuna bağlanabilmeli
- rol adı girilebilmeli
- public oyun sayfasında kadro görünmeli
- public oyuncu sayfasında oyunlar görünmeli

---

# 63. P2 — Performances / Venues

İşler:

- salon CRUD
- gösterim CRUD
- oyun → gösterim
- yaklaşan/geçmiş gösterim ayrımı
- harici bilet linki
- takvim görünümü opsiyonel

### Kabul kriterleri

- bir oyuna birden fazla gösterim bağlanabilmeli
- public sayfada yaklaşan gösterimler görünmeli
- geçmiş gösterimler ayrı gösterilebilmeli

---

# 64. P3 — News / Announcements

İşler:

- haber CRUD
- duyuru CRUD
- rich editor
- cover image
- oyun ilişkisi
- oyuncu ilişkisi
- yayın tarihi
- taslak

### Kabul kriterleri

- haber ve duyuru ayrı içerik tipi olmalı
- zaman aşımı olan duyurular ana sayfadan otomatik kalkabilmeli
- haberler oyunlarla bağlanabilmeli

---

# 65. P4 — Knowledge Center

İşler:

- articles
- categories
- tags
- related content
- rich editor
- SEO
- breadcrumbs
- internal links

### Kabul kriterleri

- makale admin panelden girilebilmeli
- kategori/tag bağlanabilmeli
- public makale sayfası SEO metadata üretmeli
- structured data bulunmalı

---

# 66. P5 — Media

İşler:

- merkezi medya kütüphanesi
- upload
- metadata
- alt text
- image resize
- WebP/AVIF
- storage abstraction

### Kabul kriterleri

- aynı görsel farklı içeriklerde kullanılabilmeli
- admin medya seçebilmelidir
- optimize edilmiş varyant üretilebilmelidir

---

# 67. P6 — Public Frontend

İşler:

- ana sayfa
- oyun listesi
- oyun detay
- oyuncu listesi
- oyuncu detay
- gösterim listesi
- haber listesi
- haber detay
- duyurular
- bilgi merkezi
- hakkımızda
- iletişim

### Kabul kriterleri

- mobile-first
- responsive
- accessibility temel testleri
- performans ölçümü
- temiz navigation

---

# 68. P7 — SEO / Performance

İşler:

- sitemap
- robots
- canonical
- Open Graph
- Twitter metadata
- structured data
- breadcrumbs
- redirect sistemi
- image optimization
- cache
- analytics

### Kabul kriterleri

- Google Search Console'a uygun sitemap
- ana içeriklerin tamamında canonical
- Event / Person / Article schema
- Lighthouse kritik hatası olmamalı

---

# 69. P8 — Production Readiness

İşler:

- security review
- integration tests
- E2E
- backup
- restore testi
- staging
- production deploy
- logs
- monitoring
- deployment dokümantasyonu

### Kabul kriterleri

- staging doğrulanmış
- migration planı doğrulanmış
- backup alınmış
- rollback yöntemi belgelenmiş
- production deploy tekrarlanabilir

---

# 70. P9 — Ticketing

P9 ayrı proje fazı olarak ele alınacaktır.

Kapsam:

- ticket types
- orders
- payment
- e-ticket
- QR
- check-in
- sales reports

P9 öncesi ödeme sağlayıcısı ayrıca seçilecektir.

---

# 71. İlk Release Kapsamı

İlk public release aşağıdakileri içermelidir:

```text
✓ Ana sayfa
✓ Oyunlar
✓ Oyuncular
✓ Oyuncu kadroları
✓ Gösterimler
✓ Haberler
✓ Duyurular
✓ Bilgi Merkezi
✓ Hakkımızda
✓ İletişim
✓ Admin panel
✓ Medya
✓ SEO
✓ Sitemap
✓ Structured data
✓ RBAC
✓ Audit log
```

Bilet satışı ilk release zorunluluğu değildir.

---

# 72. Definition of Done

Bir iş yalnızca kod yazıldığında tamamlanmış sayılmaz.

Bir özellik DONE olabilmek için:

```text
✓ Kod tamamlandı
✓ Typecheck geçti
✓ Lint geçti
✓ Test geçti
✓ Authorization kontrol edildi
✓ Validation var
✓ Error state var
✓ Loading state var
✓ Mobile kontrol edildi
✓ Accessibility kontrol edildi
✓ Gerekliyse SEO tamamlandı
✓ Migration doğrulandı
✓ Dokümantasyon güncellendi
✓ CI geçti
```

---

# 73. Branch / PR Stratejisi

Öneri:

```text
main
```

production-ready branch olacaktır.

Feature branch:

```text
feat/play-management
feat/actor-management
fix/slug-validation
```

Her anlamlı değişiklik PR ile merge edilmelidir.

---

# 74. Commit Standardı

Conventional Commits önerilir.

Örnek:

```text
feat: add play management
fix: prevent duplicate actor slugs
docs: update master plan
refactor: extract media service
test: add play service tests
```

---

# 75. Dokümantasyon

`docs/` altında zamanla:

```text
architecture.md
database.md
deployment.md
security.md
seo.md
ticketing.md
admin-guide.md
```

oluşturulabilir.

`Masterplan.md` projenin üst seviye yol haritasıdır.

---

# 76. İlk Teknik Görev Sırası

Projeye başlanırken sıralama:

```text
1. Repository bootstrap
2. Next.js + TypeScript
3. Docker + PostgreSQL
4. Prisma
5. Base schema
6. Auth
7. RBAC
8. Admin shell
9. Audit logging
10. Plays
11. Actors
12. Cast
13. Performances
14. Venues
15. News
16. Announcements
17. Articles
18. Media
19. Public frontend
20. SEO
21. Test
22. Staging
23. Production
```

---

# 77. Kritik Mimari Kurallar

1. Public sayfalar doğrudan kontrolsüz DB erişimi yapmamalıdır.
2. Authorization sadece UI seviyesinde yapılmamalıdır.
3. Admin işlemleri server-side doğrulanmalıdır.
4. Slug benzersiz olmalıdır.
5. Publish edilmeyen içerik public'te görünmemelidir.
6. İçerik silme işlemi kontrollü olmalıdır.
7. Görseller optimize edilmeden public'te kullanılmamalıdır.
8. SEO metadata sistematik üretilmelidir.
9. Oyun ile gösterim birbirinden ayrı tutulmalıdır.
10. Oyuncu ile oyun many-to-many ilişki üzerinden bağlanmalıdır.
11. Bilet sistemi mevcut content modelini bozmamalıdır.
12. Production secrets GitHub'a commit edilmemelidir.
13. Migration'lar geri izlenebilir olmalıdır.
14. Kritik admin işlemleri audit log üretmelidir.

---

# 78. İlk Veritabanı İlişki Özeti

```text
User
 └── Role / Permission

Play
 ├── PlayCast ── Actor
 ├── Performance ── Venue
 ├── News
 ├── Media
 └── Related Content

Article
 ├── Category
 ├── Tags
 ├── Media
 └── Related Content

Announcement
News
Media
SiteSettings
AuditLog
```

---

# 79. Uzun Vadeli Hedef

EylulTiyatro.com zamanla aşağıdaki yapıya dönüşebilir:

```text
Tiyatro topluluğu sitesi
+
Dijital oyun arşivi
+
Oyuncu profilleri
+
Etkinlik platformu
+
Tiyatro bilgi merkezi
+
SEO içerik platformu
+
Bilet satış sistemi
+
QR giriş sistemi
+
Topluluk başvuru sistemi
+
Dijital basın / medya arşivi
```

Ancak her özellik aynı anda yapılmayacaktır.

Temel prensip:

> **Önce doğru domain modeli ve içerik altyapısı, sonra büyüme özellikleri.**

---

# 80. İlk Milestone

İlk milestone:

## `M1 — Foundation + Core Content`

Kapsam:

```text
P0
P1
P2
```

M1 sonunda sistem:

- login olmalı
- admin panel açılmalı
- oyun oluşturulabilmeli
- oyuncu oluşturulabilmeli
- kadro kurulabilmeli
- salon oluşturulabilmeli
- gösterim eklenebilmeli
- public tarafta oyun/oyuncu/gösterim gösterilebilmelidir

Bu milestone tamamlandıktan sonra içerik platformu katmanına geçilecektir.

---

# 81. Sonuç

EylulTiyatro.com'un teknik omurgası şu şekilde sabitlenmiştir:

```text
Next.js
TypeScript
PostgreSQL
Prisma
Docker
RBAC
SEO-first architecture
Admin-managed content
Modular monolith
Future-ready ticketing
```

Bu yapı ilk aşamada gereksiz karmaşıklık yaratmadan ihtiyacı karşılar, ancak ileride bilet satışına, QR doğrulamaya, topluluk yönetimine ve daha kapsamlı tiyatro arşivine genişlemeye açıktır.
