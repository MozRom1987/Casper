# 📋 NeiroBook — Повний аналіз проекту та план реалізації

> **Дата сесії:** 03.03.2026  
> **Платформа:** Ghost CMS (self-hosted)  
> **Тема-основа:** groovy-casper (адаптація)  
> **Репозиторій:** `c:\Users\mozol\upminds_pro\academy\Casper`

---

## Зміст

1. [Концепція продукту](#концепція-продукту)
2. [Технічний стек](#технічний-стек)
3. [Структура сайту — 14 сторінок](#структура-сайту)
4. [Landing Page — детальний опис секцій](#landing-page)
5. [Ghost налаштування](#ghost-налаштування)
6. [Дизайн-система та палітра](#дизайн-система)
7. [Аналіз проблем та рішення](#аналіз-проблем)
8. [Поточний стан теми groovy-casper](#поточний-стан-теми)
9. [Що залишаємо / адаптуємо / створюємо](#що-залишаємо)
10. [Фази запуску з чеклистами](#фази-запуску)
11. [Архітектура шаблонів Ghost](#архітектура-шаблонів)
12. [Відкриті питання](#відкриті-питання)

---

## Концепція продукту

**NeiroBook** — це платформа з доступом до бібліотеки нейро-розвивальних активностей для дітей. Продукт орієнтований на батьків, які хочуть замінити екранний час структурованими іграми та вправами.

**Формат:** one-time purchase або підписка → доступ до закритого розділу з відео-активностями, PDF-картками та гайдами.

**Не є:** LMS, курс з прогресом, система сертифікатів. Це — **бібліотека контенту** з membership-доступом.

---

## Технічний стек

| Компонент | Рішення | Обґрунтування |
|---|---|---|
| **CMS / Backend** | Ghost CMS (self-hosted) | Вбудований Membership + Stripe Portal, висока швидкість (Node.js), VPS $10-15/міс |
| **Тема** | groovy-casper → адаптація | Вже розроблена база, економія часу ~40% |
| **Відеохостинг** | Bunny.net | CDN + HLS стримінг, платний але дешево, захищений embed |
| **PDF-сховище** | Google Drive (MVP) → AWS S3 | Drive = швидкий старт; S3 + Signed URLs = захист пізніше |
| **Оплата** | Stripe через Ghost Portal | Нативна інтеграція, один тариф "Academy Access" |
| **Коментарі** | Ghost Comments (вбудовані) | Тільки для members, вмикаються одним кліком |
| **Email** | Ghost + Mailgun/Postmark SMTP | Надійна доставка транзакційних email |
| **Збірка CSS/JS** | Gulp + PostCSS + Autoprefixer | Вже налаштовано в gulpfile.js |
| **Локальна розробка** | Docker Compose (порт 2369) | Live-mount теми, instant preview |

---

## Структура сайту

### 📂 Публічні сторінки (доступні всім)

| Slug | Тип Ghost | Template | Призначення |
|---|---|---|---|
| `/` | Page | `page-landing.hbs` (новий) | Landing page — повна продажна сторінка з 8 секціями |
| `/terms` | Page | `page.hbs` | Terms of Service + Release of Liability |
| `/privacy` | Page | `page.hbs` | Privacy Policy (датована 19.02.2026) |

### 🔐 Ghost Membership — системні сторінки (обов'язкові slug-и)

| Slug | Template | Призначення |
|---|---|---|
| `/subscribe` | Ghost Portal | Сторінка покупки — вибір тарифу + Stripe оплата |
| `/signin` | Ghost Portal | Вхід для існуючих покупців (magic link email) |
| `/account` | Ghost Portal | Управління акаунтом, білінг, скасування |
| `/thank-you` | `page.hbs` | Redirect після успішної оплати → потім `/welcome` |

> ⚠️ Slug-и Ghost Portal (`/subscribe`, `/signin`, `/account`) — зарезервовані Ghost і не потребують окремих Page-файлів.

### 🔒 Закриті сторінки — Members Only (після оплати)

| Slug | Назва | Тип контенту | Template |
|---|---|---|---|
| `/welcome` | Welcome / Start Here | Onboarding після першої покупки | `page-members.hbs` |
| `/academy` | Academy Dashboard | Навігація по всіх модулях | `page-members.hbs` |
| `/motor-games` | 52 Motor & Movement Games | Відео-активності | `page-module.hbs` |
| `/cognitive` | 29 Cognitive Challenges | Відео-активності | `page-module.hbs` |
| `/breathing` | 26 Breathing & Mindfulness | Відео-активності | `page-module.hbs` |
| `/stretches` | 10 Power Stretches | Відео-активності | `page-module.hbs` |
| `/cards` | Activity Cards | Завантаження 30+ PDF-карток | `page-members.hbs` |
| `/workbook` | NeuroBook Workbook | Завантаження workbook PDF | `page-members.hbs` |
| `/parents-guide` | Parents' Quick-Start Guide | Повний гайд для батьків | `page-members.hbs` |

---

## Landing Page

Сторінка `/` — це повна sales page без sidebar і blog-layout. Реалізується через кастомний шаблон `page-landing.hbs`.

### Порядок секцій

#### 1. HERO
- **H1:** [Головний заголовок — сила та емоція]
- **Subtitle:** короткий опис продукту
- **CTA Button:** "Get Instant Access" → `/subscribe`
- **Фон:** Deep Teal `#004D40` або градієнт

#### 2. PROBLEM — "Stop the Screen-Time Guilt" 🛑
- Опис болю батьків: screen-time, відсутність структурованих активностей
- Емоційний текстовий блок
- Іконки або ілюстрації проблеми

#### 3. THREE PILLARS
- 3 картки в ряд:
  - 🏃 **Motor** — рухові активності
  - 🧠 **Cognitive** — когнітивні завдання
  - ❤️ **Emotional** — емоційний розвиток
- Колір карток: білий на Soft Sand фоні

#### 4. WHAT'S INSIDE
- 4 чекбокси / feature-iтеми:
  - ✅ 117+ Videos
  - ✅ Zero Prep
  - ✅ Independent Play
  - ✅ Lifetime Access

#### 5. BONUS 🎁
- **Жовтий блок** `#FFB300`
- "30+ Activity Cards — FREE with your order"
- Іконка подарунку, візуал карток

#### 6. SOCIAL PROOF
- Цитата: **Sarah M.** ⭐⭐⭐⭐⭐
- Аватар,ім'я, текст відгуку
- Фон: Soft Sand `#F5F5F5`

#### 7. FAQ — Accordion
- 3 питання (розкриваються по кліку)
- JS: toggle class `active` на елементі
- Питання: [визначити у процесі]

#### 8. FINAL CTA
- Велика кнопка Coral: "Get Instant Access" → `/subscribe`
- Дрібний текст під кнопкою: _"All sales are final. Lifetime access."_
- Фон: Deep Teal `#004D40`

---

## Ghost налаштування

### Membership Tier
```
Ghost Admin → Settings → Tiers → New Tier
- Name: "Academy Access"
- Price: [визначити]
- Benefits: Full access to all 117+ activities
- Stripe: підключити після активації акаунту
```

### Post-Purchase Redirect
```
Ghost Admin → Settings → Portal → After sign-up redirect URL
→ /welcome
```

### Welcome Email
```
Ghost Admin → Settings → Email → Subscription confirmed email
→ Вставити текст з аркуша "Powitalny mail"
```

### Ghost Comments
```
Ghost Admin → Settings → Membership → Commenting
→ Enable comments: "Members only"
```

### Members-Only сторінки
```
Ghost Admin → кожна сторінка /academy, /motor-games тощо
→ Access: "Members only"
→ Tier: "Academy Access"
```

---

## Дизайн-система

### Кольорова палітра

| Назва | HEX | RGB | Де використовується |
|---|---|---|---|
| Deep Teal | `#004D40` | 0, 77, 64 | Заголовки, header, footer, навігація, HERO фон, FINAL CTA фон |
| Vibrant Coral | `#FF7043` | 255, 112, 67 | Всі CTA кнопки, hover-стани, виділення |
| Soft Sand | `#F5F5F5` | 245, 245, 245 | Фон сторінок, картки |
| Mustard Gold | `#FFB300` | 255, 179, 0 | Секція BONUS, акценти |
| White | `#FFFFFF` | 255, 255, 255 | Текст на темному фоні, картки |
| Dark Text | `#1A1A1A` | 26, 26, 26 | Основний текст |

> ⚠️ **Поточна тема:** використовує `#54CBCA` (бірюза Groovy) — потребує **повної заміни** на палітру NeiroBook.

### Типографіка
- **Поточна тема:** Jost + Outfit (Google Fonts)
- **Рекомендація для NeiroBook:** залишити Outfit для заголовків (дружня, кругла), додати Inter для body-тексту
- **Відповідні CSS-змінні:** визначити в `:root` в `neiro.css`

### CSS-змінні (визначити в `assets/css/neiro.css`)
```css
:root {
  --neiro-teal:    #004D40;
  --neiro-coral:   #FF7043;
  --neiro-sand:    #F5F5F5;
  --neiro-mustard: #FFB300;
  --neiro-white:   #FFFFFF;
  --neiro-text:    #1A1A1A;

  --font-heading:  'Outfit', sans-serif;
  --font-body:     'Inter', sans-serif;

  --border-radius: 12px;
  --shadow-card:   0 4px 20px rgba(0,0,0,0.08);
}
```

---

## Аналіз проблем

### 🔴 КРИТИЧНІ (вирішені)

#### 1. Ghost Pages не мають нативного захисту через Membership Tiers

**Деталь:** Ghost захищає тільки **Posts** через тарифи. Pages технічно відкриті.

**Рішення для MVP:**
```handlebars
{{! В шаблоні page-members.hbs }}
{{#unless @member}}
  <script>window.location.href = '/subscribe';</script>
{{/unless}}
```
Це приховує контент і перенаправляє незареєстрованих. Для реального продукту — достатньо.

**Альтернатива (складніша):** реалізувати `/academy` тощо як **Posts** з кастомними slug-ами замість Pages — тоді Ghost захищає їх нативно через тарифи.

---

#### 2. Ghost не хостить відео

**Деталь:** Ghost CMS — це CMS, не відеохостинг. Завантажити відео напряму неможливо.

**Рішення:** **Bunny.net**
- Завантажуємо відео на Bunny Stream
- Отримуємо `<iframe>` embed-код
- Вставляємо в Ghost Page через HTML-картку в редакторі
- Bunny надає HLS-стримінг, захист домену, аналітику

---

### 🟡 ВАЖЛИВІ (вирішені)

#### 3. PDF-завантаження

**MVP рішення:** Google Drive
1. Завантажити PDF на Google Drive
2. Отримати посилання "Anyone with link can view"
3. Вставити кнопку завантаження на members-only сторінку

**Проблема Drive:** посилання можна передати третій особі. Прийнятно для MVP.

**Рішення пізніше:** AWS S3 + Signed URLs (посилання дійсне N хвилин, не можна передати).

---

#### 4. Stripe не підключений

**Статус:** акаунт Stripe в процесі активації.

**Вплив на розробку:** нульовий — все будується паралельно. Stripe підключається в Ghost Admin одним кроком після верифікації.

**Тимчасовий стан Portal:** в режимі розробки Ghost Portal працює без реальних платежів.

---

#### 5. Поточна тема не відповідає дизайну NeiroBook

**Detalі різниці:**

| Параметр | Groovy (зараз) | NeiroBook (потрібно) |
|---|---|---|
| Основний колір | `#54CBCA` бірюза | `#004D40` Deep Teal |
| CTA колір | Dark button | `#FF7043` Coral |
| Фон | Білий | `#F5F5F5` Sand |
| Стиль | Magazine/Blog | Education/Course |
| Header | Logo + Nav + Social | Logo + "Sign In" + "Get Access" |
| Layout | Masonry blog | Landing + Members Area |

---

### 🟢 НЕЗНАЧНІ (вирішені автоматично)

#### 6. Email доставка

Ghost вбудовано підтримує email для membership-подій (підписка, відновлення пароля тощо). Для надійності — підключити **Mailgun** або **Postmark** як SMTP в Ghost Admin → Settings → Email.

---

#### 7. Коментарі до відео

Ghost Comments — вбудований модуль. Вмикається в Ghost Admin → Settings → Membership → Commenting → "Members only". Коментарі прив'язані до конкретної Ghost-сторінки/поста.

---

## Поточний стан теми

### Файлова структура `groovy-casper`

```
Casper/
├── default.hbs              ← Базовий layout (header + footer)
├── index.hbs                ← Головна (masonry сітка постів)
├── post.hbs                 ← Окрема стаття
├── page.hbs                 ← Статичні сторінки
├── tag.hbs                  ← Архів тегів
├── author.hbs               ← Архів автора
├── custom-masonry.hbs       ← Masonry layout
├── custom-authors.hbs       ← Список авторів
├── error-404.hbs            ← 404 сторінка
├── error.hbs                ← Помилки
│
├── partials/
│   ├── post-card.hbs        ← Класична blog-картка (велика)
│   ├── post-card-grid.hbs   ← Картка для masonry сітки
│   ├── sidebar.hbs          ← Бокова панель (About, Latest, Categories)
│   ├── lightbox.hbs         ← Lightbox для зображень
│   ├── svg-icons.hbs        ← SVG спрайт всіх іконок
│   └── icons/               ← 15 SVG іконок
│
├── assets/
│   ├── css/
│   │   ├── screen.css       ← ОСНОВНИЙ CSS (119KB) — вся стилістика Groovy
│   │   ├── plugins.min.css  ← Зовнішні плагіни (Font Awesome тощо)
│   │   └── global.css       ← Глобальні утиліти
│   ├── js/
│   │   ├── groovy.js        ← JS ядро (265KB) — masonry, sticky, lazy
│   │   ├── dropdown.js      ← Навігаційне меню
│   │   ├── infinite-scroll.js ← Нескінченна прокрутка
│   │   └── lightbox.js      ← Lightbox
│   ├── fonts/               ← Локальні шрифти
│   └── built/               ← Скомпільовані файли (генеруються Gulp)
│
├── gulpfile.js              ← Збірка: CSS (PostCSS), JS (concat+uglify), zip
├── package.json             ← Назва: groovy-casper v1.0.1, Ghost ≥ 5.0.0
├── docker-compose.yml       ← Локальний Ghost на порту 2369
└── dist/                    ← ZIP для завантаження в Ghost Admin
```

### `package.json` — ключові дані
```json
{
  "name": "groovy-casper",
  "version": "1.0.1",
  "engines": { "ghost": ">=5.0.0" },
  "config": {
    "posts_per_page": 10,
    "card_assets": true
  }
}
```

### `docker-compose.yml`
```yaml
services:
  ghost:
    image: ghost:5-alpine
    ports: ["2369:2368"]
    environment:
      NODE_ENV: development
      url: http://localhost:2369
    volumes:
      - ghost_data:/var/lib/ghost/content
      - .:/var/lib/ghost/content/themes/groovy-casper
```

### `default.hbs` — конфігурація теми
```javascript
var theme_config = {
    masonry: 'on',
    sticky_sidebar: 'on',
    disqus_shortname: ''
};
```

---

## Що залишаємо

### ✅ Залишаємо БЕЗ змін

| Файл / папка | Причина |
|---|---|
| `gulpfile.js` | Повністю підходить, збірка CSS/JS налаштована |
| `package.json` | Залежності актуальні, тільки змінити `name` якщо потрібно |
| `docker-compose.yml` | Локальне середовище працює |
| `assets/js/groovy.js` | JS-ядро: masonry, sticky sidebar, lazy load — потрібні |
| `assets/js/dropdown.js` | Навігація — залишаємо |
| `assets/js/lightbox.js` | Може знадобитися для відео/зображень |
| `partials/svg-icons.hbs` | Всі іконки в спрайті |
| `assets/css/plugins.min.css` | Font Awesome та інші плагіни |
| `error-404.hbs`, `error.hbs` | Залишаємо як є |

---

### ⚠️ Адаптуємо (значні правки)

| Файл | Що змінюємо |
|---|---|
| `default.hbs` | **Header:** прибираємо social кнопки, залишаємо Logo + Nav + "Sign In" + "Get Access" CTA. **Footer:** спрощуємо під NeiroBook брендинг. **CSS змінні:** бірюза → Teal. **`theme_config`:** вимкнути masonry для non-blog сторінок |
| `assets/css/screen.css` | Повна заміна палітри: `#54CBCA` → `#004D40`, всі кнопки → Coral `#FF7043`, фон → Sand `#F5F5F5`. Адаптація типографіки |
| `partials/sidebar.hbs` | Sidebar потрібен тільки для post-сторінок якщо будуть. Для course-сторінок — приховати |
| `index.hbs` | Масонованна сітка постів не потрібна для головної. Або залишити для blog-розділу, або переробити |

---

### 🆕 Створюємо з нуля

| Файл | Призначення | Пріоритет |
|---|---|---|
| `page-landing.hbs` | Landing page(`/`) з 8 секціями | 🔴 Фаза 1 |
| `assets/css/neiro.css` | CSS-змінні NeiroBook, стилі landing, members area | 🔴 Фаза 1 |
| `page-members.hbs` | Шаблон members-only сторінок з paywall-редиректом | 🟡 Фаза 2 |
| `page-module.hbs` | Шаблон відео-модулів (`/motor-games` тощо) з Bunny embed | 🟡 Фаза 2 |
| `partials/hero.hbs` | HERO секція: H1 + subtitle + CTA | 🔴 Фаза 1 |
| `partials/faq.hbs` | Accordion FAQ з 3 питаннями | 🔴 Фаза 1 |
| `partials/paywall.hbs` | Блок для незареєстрованих: "Members only, please subscribe" | 🟡 Фаза 2 |

---

## Фази запуску

### 🔴 Фаза 1 — MVP (запуск продажів)
**Ціль:** сайт онлайн, можна приймати оплату.

- [ ] Запустити Docker локально (`docker-compose up -d`)
- [ ] Встановити залежності (`yarn install`)
- [ ] Адаптувати `default.hbs` — новий header NeiroBook (Logo + "Sign In" + "Get Access")
- [ ] Оновити палітру в `screen.css` (Teal / Coral / Sand / Mustard)
- [ ] Створити `assets/css/neiro.css` з CSS-змінними NeiroBook
- [ ] Створити `partials/hero.hbs`
- [ ] Створити `partials/faq.hbs` (accordion)
- [ ] Створити `page-landing.hbs` з усіма 8 секціями
- [ ] Налаштувати Ghost: один тариф "Academy Access"
- [ ] Підключити Stripe до Ghost Portal (після активації)
- [ ] Налаштувати redirect після оплати → `/welcome`
- [ ] Верстка сторінок `/subscribe`, `/thank-you` (базово через Ghost Portal)
- [ ] Зібрати ZIP (`yarn zip`) і завантажити в Ghost Admin
- [ ] Перевірити на реальному VPS

---

### 🟡 Фаза 2 — Контент
**Ціль:** members отримують доступ до контенту після оплати.

- [ ] Створити `page-members.hbs` (layout + paywall JS-redirect)
- [ ] Створити `page-module.hbs` (layout для відео-модулів)
- [ ] Створити `partials/paywall.hbs`
- [ ] Ghost сторінки: `/welcome`, `/academy`, `/cards`, `/workbook`
- [ ] Завантажити перші відео на Bunny.net
- [ ] Embed Bunny-відео через HTML-картку в Ghost Editor
- [ ] PDF-посилання через Google Drive (hidden links)
- [ ] Welcome Email: вставити текст з `Powitalny mail` в Ghost Settings
- [ ] Підключити Mailgun/Postmark SMTP
- [ ] Тестування: купівля → email → redirect → members area

---

### 🟢 Фаза 3 — Повний курс
**Ціль:** всі відео-модулі доступні.

- [ ] Ghost сторінки: `/motor-games` (52 відео)
- [ ] Ghost сторінки: `/cognitive` (29 відео)
- [ ] Ghost сторінки: `/breathing` (26 відео)
- [ ] Ghost сторінки: `/stretches` (10 відео)
- [ ] Всі відео завантажені на Bunny.net та embedded
- [ ] Увімкнути Ghost Comments (Settings → Membership → Commenting)
- [ ] QA: перевірити всі посилання, відео, PDF

---

### 🔵 Фаза 4 — Завершення
**Ціль:** юридичні сторінки, фінальний polish.

- [ ] Ghost сторінки: `/parents-guide`
- [ ] Ghost сторінки: `/terms` (Terms of Service + Release of Liability)
- [ ] Ghost сторінки: `/privacy` (Privacy Policy, датована 19.02.2026)
- [ ] Опціонально: міграція PDF з Google Drive → AWS S3 + Signed URLs
- [ ] SEO: meta descriptions для всіх публічних сторінок
- [ ] Performance audit: PageSpeed Insights
- [ ] Фінальний ZIP → deploy на VPS

---

## Архітектура шаблонів Ghost

```
Ghost Template Hierarchy — NeiroBook
│
├── default.hbs                    ← Базовий layout (АДАПТУЄМО)
│   ├── page-landing.hbs           ← / (НОВИЙ)
│   ├── page-members.hbs           ← /welcome, /academy, /cards, /workbook, /parents-guide (НОВИЙ)
│   ├── page-module.hbs            ← /motor-games, /cognitive, /breathing, /stretches (НОВИЙ)
│   ├── page.hbs                   ← /terms, /privacy (існуючий)
│   ├── post.hbs                   ← Окремі статті якщо будуть (існуючий)
│   ├── error-404.hbs              ← 404 (існуючий)
│   └── error.hbs                  ← Помилки (існуючий)
│
└── Ghost Portal (системне)
    ├── /subscribe                 ← Ghost Portal UI
    ├── /signin                    ← Ghost Portal UI
    └── /account                   ← Ghost Portal UI
```

---

## Відкриті питання

| # | Питання | Статус | Дедлайн |
|---|---|---|---|
| 1 | Stripe — коли буде активований акаунт? | 🔄 В процесі | ASAP |
| 2 | Фінальний текст для Welcome Email (`Powitalny mail`) | ❓ Не отримано | Перед Фазою 2 |
| 3 | Ціна тарифу "Academy Access" | ❓ Не визначено | Перед підключенням Stripe |
| 4 | Hostname VPS (де буде жити сайт) | ❓ Не вказано | Перед деплоєм |
| 5 | Фінальний H1 та тексти для Landing Page | ❓ Не отримано | Перед `page-landing.hbs` |
| 6 | 3 питання для FAQ accordion | ❓ Не отримано | Перед `page-landing.hbs` |
| 7 | Цитата від Sarah M. — повний текст | ❓ Не отримано | Перед `page-landing.hbs` |
| 8 | Логотип NeiroBook (SVG або PNG) | ❓ Не отримано | Перед Фазою 1 |

---

*Документ сформовано 03.03.2026 — сесія аналізу та планування проекту NeiroBook*
