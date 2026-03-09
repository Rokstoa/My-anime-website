# 🎬 Anime Streaming Platform — Платформа для перегляду аніме

> Сучасна веб-платформа для перегляду аніме з інтеграцією Telegram бота, real-time сповіщеннями та розширеними функціями для користувачів.

<div align="center">

![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-5.0-092E20?style=for-the-badge&logo=django&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-7-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)

</div>

---

## 📋 Зміст

- [✨ Особливості](#-особливості)
- [📁 Структура Проекту](#-структура-проекту)
- [🛠️ Технології](#️-технології)
- [⚙️ Встановлення](#️-встановлення)
- [🔧 Конфігурація](#-конфігурація)
- [🚀 Запуск](#-запуск)
- [📡 API Документація](#-api-документація)
- [🤖 Telegram Бот](#-telegram-бот)
- [🗄️ Моделі Бази Даних](#️-моделі-бази-даних)
- [🔔 WebSocket & Сповіщення](#-websocket--сповіщення)
- [⚡ Celery Tasks](#-celery-tasks)
- [🎨 Адмін-панель](#-адмін-панель)
- [🧪 Тестування](#-тестування)
- [🚢 Деплой](#-деплой)

---

## ✨ Особливості

### Для користувачів
| Функція | Опис |
|---------|------|
| 🎥 HLS Стрімінг | Перегляд аніме у高якості з адаптивним бітрейтом |
| 📝 Списки перегляду | 6 статусів: Дивлюсь / Переглянуто / Відкладено / Покинуто / Планую / Передивляюсь |
| 💬 Коментарі | Threaded система з реакціями та захистом від спойлерів |
| ⭐ Оцінки | Система оцінювання 1–10 з агрегованим рейтингом |
| 🔔 Сповіщення | Real-time через WebSocket + Telegram |
| 📱 Telegram Bot | Пошук, управління списками, сповіщення |
| 💾 Прогрес | Збереження позиції перегляду для кожної серії |
| 🏆 Досягнення | Система нагород за активність |
| 🎯 Рекомендації | Персоналізовані рекомендації на основі перегляду |
| 🔐 OAuth | Вхід через Google та Discord |

### Технічні можливості
| Функція | Технологія |
|---------|-----------|
| REST API | DRF + JWT авторизація |
| Real-time | Django Channels + Redis |
| Фонові задачі | Celery + Celery Beat |
| Пошук | Full-text search + inline Telegram |
| Кешування | Redis із автоінвалідацією |
| Медіа | Автоконвертація у WebP, thumbnails |
| Деплой | Docker Compose + Nginx + SSL |

---

## 📁 Структура Проекту

```
animex/
│
├── 📁 config/                          # ⚙️ Конфігурація Django
│   ├── 📁 settings/
│   │   ├── base.py                     # Базові налаштування (спільні)
│   │   ├── development.py              # Dev: SQLite, debug toolbar, console email
│   │   └── production.py              # Prod: PostgreSQL, S3, Sentry, SSL
│   ├── urls.py                         # Кореневі URL маршрути
│   ├── asgi.py                         # ASGI + Django Channels (WebSocket)
│   ├── wsgi.py                         # WSGI для Gunicorn
│   └── celery.py                       # Celery app + розклад periodic tasks
│
├── 📁 apps/
│   │
│   ├── 📁 anime/                       # 🎬 Ядро — аніме каталог
│   │   ├── models.py                   # Anime, Episode, Genre, Studio,
│   │   │                               # Translation, VideoSource, UserRating,
│   │   │                               # Screenshot, RelatedAnime
│   │   ├── admin.py                    # Jazzmin-адмінка з прев'ю обкладинок,
│   │   │                               # batch-публікацією та авто-сповіщеннями
│   │   ├── views.py                    # Каталог, деталі, плеєр, рейтинги
│   │   ├── urls.py
│   │   ├── filters.py                  # Фільтри каталогу (жанр, рік, тип...)
│   │   ├── signals.py                  # Тригери на оновлення рейтингу
│   │   ├── context_processors.py       # Глобальний контекст шаблонів
│   │   ├── sitemaps.py                 # XML Sitemap для SEO
│   │   ├── 📁 migrations/
│   │   └── 📁 templatetags/
│   │       ├── anime_tags.py           # {% rating_stars %}, {% watch_progress %}
│   │       └── player_tags.py          # {% embed_player %}
│   │
│   ├── 📁 users/                       # 👤 Користувачі та профілі
│   │   ├── models.py                   # User (AbstractUser), UserAchievement,
│   │   │                               # TelegramLinkToken
│   │   ├── admin.py
│   │   ├── views.py                    # Профіль, налаштування, підписки
│   │   ├── urls.py
│   │   ├── forms.py                    # Реєстрація, редагування профілю
│   │   ├── serializers.py
│   │   └── 📁 migrations/
│   │
│   ├── 📁 watchlist/                   # 📋 Списки перегляду
│   │   ├── models.py                   # WatchList, EpisodeProgress, Collection,
│   │   │                               # CollectionItem
│   │   ├── views.py                    # CRUD списків, прогрес серій
│   │   ├── urls.py
│   │   ├── serializers.py
│   │   └── 📁 migrations/
│   │
│   ├── 📁 comments/                    # 💬 Коментарі
│   │   ├── models.py                   # Comment (MPTT), CommentReaction,
│   │   │                               # CommentReport
│   │   ├── views.py                    # Додавання, редагування, реакції
│   │   ├── urls.py
│   │   ├── serializers.py
│   │   └── 📁 migrations/
│   │
│   ├── 📁 notifications/               # 🔔 Сповіщення
│   │   ├── models.py                   # Notification, NotificationPreference
│   │   ├── consumers.py                # WebSocket Consumer (real-time push)
│   │   ├── routing.py                  # WS URL patterns
│   │   ├── services.py                 # Логіка відправки (site/telegram/email)
│   │   ├── views.py
│   │   ├── urls.py
│   │   └── 📁 migrations/
│   │
│   ├── 📁 search/                      # 🔍 Пошук
│   │   ├── views.py                    # Full-text пошук по базі
│   │   ├── urls.py
│   │   └── 📁 migrations/
│   │
│   └── 📁 api/
│       └── 📁 v1/                      # 📡 REST API v1
│           ├── urls.py                 # Router + auth endpoints
│           ├── views.py                # AnimeViewSet, EpisodeViewSet,
│           │                           # WatchListViewSet, CommentViewSet,
│           │                           # NotificationViewSet, UserProfileViewSet
│           ├── serializers.py          # DRF serializers з вкладеними даними
│           ├── filters.py              # API фільтри
│           ├── permissions.py          # IsAuthorOrReadOnly, IsVerified
│           └── pagination.py           # StandardResultsPagination (24/сторінку)
│
├── 📁 bot/                             # 🤖 Telegram Bot (aiogram 3)
│   ├── main.py                         # Dispatcher + middlewares + polling/webhook
│   ├── urls.py                         # Django URL для webhook endpoint
│   │
│   ├── 📁 handlers/
│   │   ├── start_handler.py            # /start, /help, deep link прив'язка
│   │   ├── anime_handler.py            # Перегляд аніме, деталі, рейтинги
│   │   ├── search_handler.py           # Пошук (inline mode @bot query)
│   │   ├── watchlist_handler.py        # Управління списком перегляду
│   │   ├── notifications_handler.py    # Налаштування сповіщень
│   │   └── account_handler.py          # Профіль, статистика
│   │
│   ├── 📁 keyboards/
│   │   ├── main_menu.py                # Reply keyboard (головне меню)
│   │   └── inline.py                   # Inline keyboards (кнопки до повідомлень)
│   │
│   ├── 📁 middlewares/
│   │   ├── auth.py                     # Авто-завантаження Django User по tg_id
│   │   ├── ban_check.py                # Перевірка бану
│   │   └── throttling.py               # Обмеження частоти запитів
│   │
│   └── 📁 utils/
│       ├── text_formatter.py           # HTML форматування для Telegram
│       └── pagination.py               # Пагінація результатів у боті
│
├── 📁 celery/                          # ⚡ Фонові завдання
│   ├── tasks.py                        # Celery tasks (сповіщення, статистика)
│   └── schedules.py                    # Розклад periodic tasks
│
├── 📁 templates/                       # 🖼️ HTML шаблони
│   ├── base.html                       # Базовий шаблон
│   ├── 📁 anime/
│   │   ├── catalog.html                # Каталог з фільтрами
│   │   ├── detail.html                 # Сторінка аніме
│   │   └── player.html                 # Відеоплеєр
│   ├── 📁 users/
│   │   ├── profile.html
│   │   └── settings.html
│   ├── 📁 watchlist/
│   │   └── list.html
│   ├── 📁 notifications/
│   │   └── list.html
│   ├── 📁 includes/                    # Часткові шаблони (navbar, cards...)
│   └── 📁 emails/                      # Email шаблони
│       ├── base_email.html
│       ├── new_episode.html
│       └── weekly_digest.html
│
├── 📁 static/
│   ├── 📁 css/
│   │   ├── main.css                    # Основні стилі
│   │   ├── player.css                  # Стилі плеєра
│   │   └── admin_custom.css            # Кастомізація Jazzmin
│   ├── 📁 js/
│   │   ├── player.js                   # HLS відеоплеєр (hls.js)
│   │   ├── notifications.js            # WebSocket клієнт
│   │   ├── watchlist.js                # AJAX управління списком
│   │   └── comments.js                 # AJAX коментарі
│   └── 📁 img/
│       └── logo.png
│
├── 📁 media/                           # Завантажені медіафайли (gitignored)
│   ├── 📁 anime/
│   │   ├── 📁 covers/                  # Обкладинки (400×560, WebP)
│   │   ├── 📁 banners/                 # Банери (1920×400, WebP)
│   │   └── 📁 screenshots/             # Скріншоти
│   └── 📁 avatars/                     # Аватари користувачів
│
├── 📁 docker/
│   ├── Dockerfile                      # Образ Django/Celery/Bot
│   ├── docker-compose.yml              # Всі сервіси разом
│   └── nginx.conf                      # Nginx конфіг (SSL, proxy, gzip)
│
├── 📁 requirements/
│   ├── base.txt                        # Django, DRF, aiogram, Celery...
│   ├── development.txt                 # debug-toolbar, pytest, black...
│   └── production.txt                  # gunicorn, whitenoise, sentry-sdk...
│
├── 📁 docs/                            # Документація проекту
│   ├── api.md
│   └── deployment.md
│
├── .env.example                        # Приклад змінних середовища
├── manage.py
└── README.md
```

---

## 🛠️ Технології

| Категорія | Стек |
|-----------|------|
| **Backend** | Django 5.0, Django REST Framework 3.15 |
| **Database** | PostgreSQL 16 |
| **Cache / Broker** | Redis 7 |
| **Task Queue** | Celery 5.3 + django-celery-beat |
| **WebSockets** | Django Channels 4 + channels-redis |
| **Telegram Bot** | aiogram 3.4 (async) |
| **Auth** | JWT (simplejwt) + OAuth (Google, Discord) via allauth |
| **API Docs** | drf-spectacular (Swagger UI + ReDoc) |
| **Admin UI** | django-jazzmin (тема Darkly) |
| **Images** | django-imagekit (WebP, thumbnails) |
| **Comments** | django-mptt (деревоподібна структура) |
| **Search** | PostgreSQL full-text search |
| **Video** | HLS.js (плеєр) + Kodik/YouTube embed |
| **Containerization** | Docker + Docker Compose + Nginx |
| **Monitoring** | Sentry SDK |
| **CI/CD** | GitHub Actions |

---

## ⚙️ Встановлення

### Варіант 1 — Docker (рекомендовано)

```bash
# 1. Клонувати репозиторій
git clone https://github.com/your-username/animex.git
cd animex

# 2. Налаштувати змінні середовища
cp .env.example .env
nano .env  # заповни необхідні значення

# 3. Запустити всі сервіси
cd docker
docker-compose up -d --build

# 4. Виконати міграції
docker-compose exec web python manage.py migrate

# 5. Створити суперкористувача
docker-compose exec web python manage.py createsuperuser

# 6. Зібрати статику
docker-compose exec web python manage.py collectstatic --noinput
```

### Варіант 2 — Локально (для розробки)

```bash
# 1. Python virtual environment
python -m venv venv
source venv/bin/activate          # Linux/macOS
# venv\Scripts\activate           # Windows

# 2. Залежності
pip install -r requirements/development.txt

# 3. Змінні середовища
cp .env.example .env

# 4. База даних і міграції
python manage.py migrate

# 5. Тестові дані (опціонально)
python manage.py loaddata fixtures/sample_anime.json

# 6. Запуск сервера
python manage.py runserver
```

> ⚠️ Для повного функціоналу потрібні запущені **Redis** та **PostgreSQL**.  
> Встанови через Docker: `docker run -d -p 6379:6379 redis:7-alpine`

---

## 🔧 Конфігурація

Скопіюй `.env.example` → `.env` та заповни значення:

```env
# ── Django ────────────────────────────────────────
SECRET_KEY=your-secret-key-minimum-50-characters
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# ── Database ──────────────────────────────────────
DATABASE_URL=postgres://animex_user:password@localhost:5432/animex_db

# ── Redis ─────────────────────────────────────────
REDIS_URL=redis://localhost:6379/0

# ── Telegram Bot ──────────────────────────────────
TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyz
TELEGRAM_WEBHOOK_URL=https://animex.ua/bot/webhook/

# ── Email ─────────────────────────────────────────
EMAIL_HOST=smtp.gmail.com
EMAIL_HOST_USER=your-email@gmail.com
EMAIL_HOST_PASSWORD=your-app-password

# ── OAuth ─────────────────────────────────────────
GOOGLE_CLIENT_ID=...
DISCORD_CLIENT_ID=...

# ── Sentry ────────────────────────────────────────
SENTRY_DSN=https://xxx@sentry.io/xxx
```

### Налаштування бота

1. Створи бота через [@BotFather](https://t.me/BotFather) — отримай `TELEGRAM_BOT_TOKEN`
2. Встанови команди у BotFather:
```
start - Головне меню
search - Пошук аніме
watchlist - Мій список перегляду
profile - Мій профіль
notifications - Налаштування сповіщень
top - Топ аніме
random - Випадкове аніме
help - Довідка
```
3. Увімкни **Inline Mode** для пошуку через `@animex_bot запит`

---

## 🚀 Запуск

### Development (усі компоненти)

Відкрий **4 термінали**:

```bash
# Terminal 1 — Django
python manage.py runserver

# Terminal 2 — Celery worker
celery -A config worker -l info --concurrency=4

# Terminal 3 — Celery beat (scheduler)
celery -A config beat -l info

# Terminal 4 — Telegram bot (polling)
python -m bot.main
```

### Production (Docker)

```bash
cd docker
docker-compose up -d

# Моніторинг логів
docker-compose logs -f web
docker-compose logs -f celery_worker
docker-compose logs -f telegram_bot

# Перезапуск одного сервісу
docker-compose restart web
```

### Корисні команди

```bash
# Скинути кеш
python manage.py shell -c "from django.core.cache import cache; cache.clear()"

# Оновити рейтинги вручну
python manage.py shell -c "from celery.tasks import update_anime_scores; update_anime_scores()"

# Встановити webhook для бота
python manage.py set_telegram_webhook

# Імпорт аніме з MyAnimeList (кастомна команда)
python manage.py import_mal --id 21  # One Piece
```

---

## 📡 API Документація

Інтерактивна документація доступна після запуску:

| Інтерфейс | URL |
|-----------|-----|
| **Swagger UI** | `http://localhost:8000/api/schema/swagger-ui/` |
| **ReDoc** | `http://localhost:8000/api/schema/redoc/` |
| **OpenAPI JSON** | `http://localhost:8000/api/schema/` |

### Основні ендпоінти

#### Аутентифікація
```http
POST /api/v1/auth/token/           # Отримати JWT (email + password)
POST /api/v1/auth/token/refresh/   # Оновити access token
POST /api/v1/auth/registration/    # Реєстрація
```

#### Аніме
```http
GET  /api/v1/anime/                        # Каталог (фільтри, сортування, пагінація)
GET  /api/v1/anime/{slug}/                 # Деталі аніме
GET  /api/v1/anime/{slug}/episodes/        # Список серій
GET  /api/v1/anime/{slug}/episodes/{n}/    # Деталі серії + джерела відео
POST /api/v1/anime/{slug}/rate/            # Поставити оцінку

# Фільтри каталогу:
# ?genre=action&year=2024&type=tv&status=ongoing&ordering=-score_site
```

#### Список перегляду
```http
GET    /api/v1/watchlist/          # Мій список (авторизація обов'язкова)
POST   /api/v1/watchlist/          # Додати/оновити запис
DELETE /api/v1/watchlist/{id}/     # Видалити

# Оновити прогрес серії:
POST /api/v1/watchlist/progress/
{
  "episode_id": 123,
  "position": 840,        # секунди
  "is_watched": false
}
```

#### Коментарі
```http
GET  /api/v1/comments/?anime={slug}         # Коментарі до аніме
GET  /api/v1/comments/?episode={id}         # Коментарі до серії
POST /api/v1/comments/                       # Додати коментар
POST /api/v1/comments/{id}/react/            # Лайк / дизлайк
POST /api/v1/comments/{id}/report/           # Поскаржитись
```

#### Сповіщення
```http
GET  /api/v1/notifications/                  # Мої сповіщення
POST /api/v1/notifications/mark-all-read/    # Позначити всі як прочитані
```

### Приклад запиту

```bash
# Отримати JWT
curl -X POST http://localhost:8000/api/v1/auth/token/ \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "secret"}'

# Запит з авторизацією
curl http://localhost:8000/api/v1/watchlist/ \
  -H "Authorization: Bearer <access_token>"
```

---

## 🤖 Telegram Бот

### Команди

| Команда | Опис |
|---------|------|
| `/start` | Головне меню |
| `/search <назва>` | Пошук аніме |
| `/watchlist` | Переглянути свій список |
| `/top` | Топ аніме за рейтингом |
| `/random` | Випадкове аніме |
| `/profile` | Статистика перегляду |
| `/notifications` | Налаштування сповіщень |
| `/link` | Прив'язати акаунт AnimeX |

### Inline режим

Пошук прямо в будь-якому чаті без переходу в бота:

```
@animex_bot Наруто
@animex_bot Attack on Titan
@animex_bot ваншот
```

### Прив'язка акаунта

Щоб отримувати персональні сповіщення:

1. Зайди на сайт AnimeX → **Налаштування** → **Telegram**
2. Натисни **«Прив'язати Telegram»**
3. Перейди за згенерованим посиланням
4. Бот підтвердить прив'язку

```
Посилання має вигляд:
https://t.me/animex_bot?start=link_xxxxxxxxxxxxxxxx
```

### Типи сповіщень

| Подія | Сайт | Telegram | Email |
|-------|------|----------|-------|
| Нова серія | ✅ | ✅ (налаштовується) | ✅ (дайджест) |
| Відповідь на коментар | ✅ | ✅ | ❌ |
| Нове досягнення | ✅ | ❌ | ❌ |
| Завершення аніме | ✅ | ✅ | ❌ |
| Системні оголошення | ✅ | ✅ | ✅ |

---

## 🗄️ Моделі Бази Даних

### ER-діаграма (спрощена)

```
User ──────────────── WatchList ──── Anime
 │                                    │   │
 ├── EpisodeProgress ── Episode ──────┘   │
 │                         │              │
 ├── Comment ──────────────┘          Genre (M2M)
 │     └── CommentReaction            Studio (M2M)
 │                                    Author (M2M)
 ├── UserRating ─────────── Anime         │
 │                                    Translation
 ├── Notification                         │
 │                                    VideoSource
 ├── UserAchievement
 │
 └── TelegramLinkToken
```

### Ключові моделі

**`Anime`** — центральна модель з полями:
`title_ua/en/jp`, `slug`, `cover`, `banner`, `anime_type`, `status`, `year`, `season`, `episodes_total`, `score_site`, `views_count`, `mal_id`, `anilist_id`, `is_published`, `is_featured`, `is_adult`

**`Episode`** — серії:
`anime (FK)`, `number`, `title`, `air_date`, `duration`, `is_filler`, `views_count`

**`VideoSource`** — джерела відео:
`episode (FK)`, `hosting` (Kodik/YouTube/Internal...), `quality` (1080p/720p/480p), `url`, `translation (FK)`

**`WatchList`** — список перегляду:
`user`, `anime`, `status` (6 варіантів), `score`, `episodes_watched`, `notes`, `is_favorite`

---

## 🔔 WebSocket & Сповіщення

### Підключення на frontend

```javascript
// Підключення до WS для real-time сповіщень
const protocol = location.protocol === 'https:' ? 'wss:' : 'ws:';
const ws = new WebSocket(`${protocol}//${location.host}/ws/notifications/`);

ws.onmessage = (event) => {
    const data = JSON.parse(event.data);

    switch (data.type) {
        case 'notification':
            showToast(data.title, data.message, data.link);
            updateUnreadBadge();
            break;
        case 'unread_count':
            setUnreadBadge(data.count);
            break;
        case 'all_read':
            clearUnreadBadge();
            break;
    }
};

// Позначити сповіщення прочитаним
ws.send(JSON.stringify({
    action: 'mark_read',
    notification_id: 42
}));

// Позначити всі прочитаними
ws.send(JSON.stringify({ action: 'mark_all_read' }));
```

### URL WebSocket

```
ws://localhost:8000/ws/notifications/
```

---

## ⚡ Celery Tasks

### Автоматичні задачі

| Задача | Тригер | Опис |
|--------|--------|------|
| `send_new_episode_notifications` | При публікації серії | Сповіщення всім у watchlist |
| `push_notification_to_ws` | Після створення Notification | WebSocket push |
| `send_telegram_episode_notification` | При публікації серії | Повідомлення в Telegram |
| `update_anime_scores` | Кожні 30 хв | Перерахунок рейтингів |
| `update_user_stats` | Щогодини | Статистика переглядів |
| `cleanup_expired_tokens` | Кожні 30 хв | Видалення старих токенів |
| `send_weekly_digest` | Щопонеділка 10:00 | Email дайджест |

### Черги Celery

```bash
# Запуск з окремими чергами
celery -A config worker -l info -Q default,notifications,emails --concurrency=4
```

---

## 🎨 Адмін-панель

Доступна за адресою `/admin/` з темою **Darkly** (Jazzmin).

### Можливості

- 🖼️ Прев'ю обкладинок прямо в списку аніме
- ⚡ Batch-дії: публікація, знімання з публікації, додавання на головну
- 📊 Фільтри: жанр, рік, тип, статус, чи опубліковано
- 🔔 Автоматичне надсилання Celery-задач при публікації серій
- 📱 Адаптивний інтерфейс

### Налаштування теми

```python
# config/settings/base.py
JAZZMIN_UI_TWEAKS = {
    "theme": "darkly",          # або: cyborg, slate, superhero
    "sidebar": "sidebar-dark-purple",
    "navbar": "navbar-dark",
    "accent": "accent-purple",
}
```

---

## 🧪 Тестування

```bash
# Запустити всі тести
pytest

# З покриттям коду
pytest --cov=apps --cov-report=html

# Тільки конкретний модуль
pytest apps/anime/tests.py -v

# Тести API
pytest apps/api/ -v

# Тести бота
pytest bot/tests/ -v --asyncio-mode=auto
```

### Структура тестів

```
apps/
  anime/tests/
    test_models.py
    test_views.py
    test_api.py
  users/tests/
  watchlist/tests/
bot/tests/
  test_handlers.py
  test_middlewares.py
```

---

## 🚢 Деплой

### Docker Compose (production)

```
┌─────────────────────────────────────────────┐
│                   Nginx :80/:443             │
│         (SSL termination, gzip, cache)       │
└────────────┬──────────────┬─────────────────┘
             │              │
     ┌───────▼──────┐  ┌────▼────────┐
     │  Django :8000 │  │ ASGI :8001  │
     │  (Gunicorn)   │  │ (Daphne/WS) │
     └───────┬───────┘  └─────┬───────┘
             │                │
     ┌───────▼────────────────▼───────┐
     │           PostgreSQL           │
     │           Redis                │
     └────────────────────────────────┘
             │                │
     ┌───────▼───────┐ ┌──────▼────────┐
     │ Celery Worker │ │  Celery Beat   │
     │ Celery Worker │ └───────────────┘
     └───────────────┘
             │
     ┌───────▼───────┐
     │ Telegram Bot  │
     └───────────────┘
```

### SSL (Let's Encrypt)

```bash
# Автоматичний SSL через certbot
docker-compose -f docker-compose.yml -f docker-compose.ssl.yml up -d certbot
```

### Змінні для production

```env
DEBUG=False
ALLOWED_HOSTS=animex.ua,www.animex.ua
DATABASE_URL=postgres://user:pass@db:5432/animex_db
AWS_STORAGE_BUCKET_NAME=animex-media    # для медіа на S3
SENTRY_DSN=https://...@sentry.io/...
```

---

## 📊 Статистика проекту

| Метрика | Значення |
|---------|----------|
| Моделей БД | 20+ |
| API ендпоінтів | 40+ |
| Celery tasks | 7 |
| Команди бота | 8 |
| Типів сповіщень | 8 |
| Сервісів Docker | 8 |

---

<div align="center">

Made with ❤️ for anime fans 🌸

</div>
