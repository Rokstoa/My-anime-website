```markdown
# 🎬 Anime Streaming Platform - Платформа для перегляду аніме

Сучасна веб-платформа для перегляду аніме з інтеграцією Telegram бота, real-time сповіщеннями та розширеними функціями для користувачів.

## 📋 Зміст

- [Особливості](#особливості)
- [Структура Проекту](#структура-проекту)
- [Технології](#технології)
- [Встановлення](#встановлення)
- [Конфігурація](#конфігурація)
- [Запуск](#запуск)
- [API Документація](#api-документація)
- [Telegram Бот](#telegram-бот)

---

## ✨ Особливості

### Для користувачів:
- 🎥 Перегляд аніме у високій якостості (HLS стрімінг)
- 📝 Персональні списки перегляду (6 статусів)
- 💬 Коментарі з threading (деревоподібна структура)
- ⭐ Оцінки та рецензії на аніме
- 🔔 Real-time сповіщення через WebSocket
- 📱 Інтеграція з Telegram
- 🎯 Персональні рекомендації
- 💾 Збереження прогресу перегляду

### Технічні можливості:
- REST API з JWT авторизацією
- Real-time оновлення через Channels
- Фонові задачі через Celery
- Автоматичні сповіщення про нові серії
- Пошук через Telegram бота
- HLS відео плеєр власної розробки

---

## 📁 Структура Проекту

```

My-anime-website/
├── config/                          # Головна конфігурація Django проекту
│   ├── asgi.py                     # ASGI конфігурація для WebSocket та Channels
│   ├── settings.py                 # Основні налаштування проекту
│   ├── urls.py                     # Головні URL маршрути
│   └── wsgi.py                     # WSGI конфігурація для production
│
├── apps/                           # Django додатки
│   │
│   ├── anime/                      # Додаток аніме
│   │   ├── models.py              # Моделі: Anime, Episode, Genre, Studio, Translation, VideoSource, UserRating
│   │   ├── views.py               # Views для перегляду аніме
│   │   ├── urls.py                # URL маршрути аніме
│   │   ├── admin.py               # Admin панель для аніме
│   │   └── services/              # Бізнес-логіка та сервіси
│   │
│   ├── users/                      # Додаток користувачів
│   │   ├── models.py              # Кастомна модель User з Telegram-прив'язкою
│   │   │                          # Achievement - досягнення користувачів
│   │   │                          # UserSettings - налаштування користувача
│   │   ├── views.py               # Реєстрація, профіль, налаштування
│   │   ├── serializers.py         # Серіалізатори для API
│   │   └── admin.py               # Admin панель користувачів
│   │
│   ├── watchlist/                  # Додаток списків перегляду
│   │   ├── models.py              # WatchList (6 статусів)
│   │   │                          # EpisodeProgress - збереження позиції перегляду
│   │   │                          # Collection - колекції аніме
│   │   ├── views.py               # Управління списками
│   │   └── admin.py               # Admin панель
│   │
│   ├── comments/                   # Додаток коментарів
│   │   ├── models.py              # Comment (MPTT дерево)
│   │   │                          # Reaction - реакції на коментарі
│   │   │                          # CommentReport - скарги на коментарі
│   │   ├── views.py               # CRUD для коментарів
│   │   └── admin.py               # Модерація коментарів
│   │
│   ├── notifications/              # Додаток сповіщень
│   │   ├── models.py              # Notification - сповіщення
│   │   │                          # WebSocketMessage - WebSocket повідомлення
│   │   ├── consumers.py           # WebSocket Consumer для real-time
│   │   ├── tasks.py               # Завдання для відправки сповіщень
│   │   └── admin.py               # Admin панель сповіщень
│   │
│   └── api/                        # API додаток
│       ├── views.py               # API ViewSets: AnimeViewSet, EpisodeViewSet, WatchListViewSet
│       │                          # CommentViewSet, NotificationViewSet, SearchView
│       ├── serializers.py         # API серіалізатори
│       ├── urls.py                # API URL маршрути
│       ├── permissions.py         # Кастомні дозволи
│       └── pagination.py          # Кастомна пагінація
│
├── bot/                            # Telegram бот на aiogram 3
│   ├── main.py                    # Точка входу бота
│   ├── handlers/                  # Обробники команд
│   │   ├── start.py              # /start команда
│   │   ├── search.py             # Inline search
│   │   ├── account.py            # Прив'язка акаунта
│   │   └── notifications.py      # Сповіщення
│   ├── keyboards/                 # Inline та Reply клавіатури
│   └── services/                  # Сервіси для взаємодії з Django
│
├── celery_tasks/                   # Celery фонові задачі
│   ├── tasks.py                   # Завдання:
│   │                              # - Авто-сповіщення при публікації серії
│   │                              # - WebSocket push через Channels
│   │                              # - Оновлення рейтингів (кожні 30 хв)
│   │                              # - Тижневий email-дайджест
│   └── celery_schedule.py         # Розклад задач (Celery Beat)
│
├── templates/                      # HTML шаблони
│   ├── base.html                  # Базовий шаблон
│   ├── anime/                     # Шаблони аніме
│   ├── users/                     # Шаблони користувачів
│   └── components/                # Компоненти (частини шаблонів)
│
├── static/                         # Статичні файли
│   ├── css/                       # CSS стилі
│   ├── js/                        # JavaScript файли
│   ├── images/                    # Зображення
│   └── fonts/                     # Шрифти
│
├── media/                          # Медіа файли (завантаження користувачів)
│   ├── anime/                     # Обкладинки та банери аніме
│   ├── episodes/                  # Відео файли епізодів
│   ├── users/                     # Аватарки користувачів
│   └── subtitles/                 # Субтитри
│
├── docker/                         # Docker конфігурації
│   ├── nginx/                     # Nginx конфігурація
│   ├── postgres/                  # PostgreSQL ініціалізація
│   └── redis/                     # Redis конфігурація
│
├── docker-compose.yml              # Docker Compose конфігурація
├── requirements.txt                # Python залежності
├── .env                           # Змінні оточення
└── manage.py                      # Django management script
```
---

## 🛠 Технології

### Backend:
- **Django 4.2** - веб-фреймворк
- **Django REST Framework** - побудова API
- **SimpleJWT** - JWT авторизація
- **Channels** - WebSocket підтримка
- **Celery** - фонові задачі
- **Redis** - кешування та broker для Celery
- **PostgreSQL** - основна база даних
- **django-mptt** - дерева для коментарів

### Frontend:
- **Bootstrap 5** - UI фреймворк
- **HTMX** - динамічні запити без JavaScript
- **Vanilla JS** - мінімум JavaScript

### Bot:
- **aiogram 3** - асинхронний Telegram bot фреймворк

### DevOps:
- **Docker & Docker Compose** - контейнеризація
- **Nginx** - reverse proxy
- **Gunicorn/Daphne** - WSGI/ASGI сервери

---

## 📦 Встановлення

### Вимоги:
- Python 3.10+
- PostgreSQL 14+ (або SQLite для розробки)
- Redis 6+
- Docker (опціонально)

### 1. Клонування репозиторію

```
bash
git clone <repository-url>
cd My-anime-website
```
### 2. Створення віртуального оточення

```
bash
# Windows
python -m venv .venv
.venv\Scripts\activate

# Linux/Mac
python3 -m venv venv
source venv/bin/activate
```
### 3. Встановлення залежностей

```
bash
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
```
### 4. Налаштування бази даних

#### Варіант A: SQLite (для розробки)

Налаштування вже є в `config/settings.py`:

```
python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```
#### Варіант B: PostgreSQL (production)

Створіть `.env` файл:

```
env
POSTGRES_DB=anime_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password
POSTGRES_HOST=db
POSTGRES_PORT=5432
```
### 5. Міграції

```
bash
python manage.py makemigrations
python manage.py migrate
```
### 6. Створення суперкористувача

```
bash
python manage.py createsuperuser
```
---

## ⚙️ Конфігурація

### Змінні оточення (.env)

```
env
# Django
DEBUG=True
DJANGO_SECRET_KEY=your-secret-key-here
ALLOWED_HOSTS=localhost,127.0.0.1

# Database
POSTGRES_DB=anime_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=secure_password
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

# Redis
REDIS_URL=redis://localhost:6379/0

# Celery
CELERY_BROKER_URL=redis://localhost:6379/1
CELERY_RESULT_BACKEND=redis://localhost:6379/2

# Telegram Bot
TELEGRAM_BOT_TOKEN=your-bot-token
TELEGRAM_WEBHOOK_URL=https://yourdomain.com/bot/webhook/

# Email
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_HOST_USER=your-email@gmail.com
EMAIL_HOST_PASSWORD=your-app-password

# CORS
CORS_ALLOWED_ORIGINS=http://localhost:3000,https://yourdomain.com
```
---

## 🚀 Запуск

### Розробка

#### 1. Запуск Django сервера

```
bash
python manage.py runserver
```
#### 2. Запуск Redis (Windows)

Завантажте Redis для Windows або використовуйте Docker:

```
bash
docker run -d -p 6379:6379 redis:latest
```
#### 3. Запуск Celery Worker

```
bash
# Windows
celery -A config worker --pool=solo --loglevel=info

# Linux/Mac
celery -A config worker --loglevel=info
```
#### 4. Запуск Celery Beat (планувальник)

```
bash
celery -A config beat --loglevel=info
```
#### 5. Запуск Daphne (ASGI для WebSocket)

```
bash
daphne -b 0.0.0.0 -p 8001 config.asgi:application
```
### Production з Docker

```
bash
# Збірка та запуск всіх контейнерів
docker-compose up -d --build

# Перегляд логів
docker-compose logs -f

# Зупинка
docker-compose down
```
### Доступні сервіси в Docker:
- **Django** - http://localhost:8000
- **PostgreSQL** - localhost:5432
- **Redis** - localhost:6379
- **pgAdmin** - http://localhost:8080
- **Flower (Celery monitor)** - http://localhost:5555

---

## 📡 API Документація

Після запуску сервера доступна повна API документація:

### Swagger UI
```

http://localhost:8000/api/schema/swagger-ui/
```
### ReDoc
```

http://localhost:8000/api/docs/
```
### OpenAPI Schema
```

http://localhost:8000/api/schema/
```
### Основні Endpoint:

#### Аніме
- `GET /api/v1/anime/` - список всіх аніме
- `GET /api/v1/anime/{id}/` - деталі аніме
- `GET /api/v1/anime/featured/` - популярні аніме
- `GET /api/v1/anime/trending/` - тренди
- `GET /api/v1/anime/top_rated/` - найвищий рейтинг
- `POST /api/v1/anime/{id}/rate/` - оцінити аніме

#### Епізоди
- `GET /api/v1/episodes/` - список епізодів
- `POST /api/v1/episodes/{id}/update_progress/` - оновити прогрес перегляду

#### Списки перегляду
- `GET /api/v1/watchlist/` - мій список
- `POST /api/v1/watchlist/` - додати до списку
- `GET /api/v1/watchlist/by_status/` - фільтр по статусу

#### Коментарі
- `GET /api/v1/comments/` - всі коментарі
- `POST /api/v1/comments/` - створити коментар
- `POST /api/v1/comments/{id}/like/` - вподобати
- `POST /api/v1/comments/{id}/report/` - поскаржитися

#### Сповіщення
- `GET /api/v1/notifications/` - мої сповіщення
- `POST /api/v1/notifications/{id}/mark_read/` - прочитати
- `POST /api/v1/notifications/mark_all_read/` - прочитати все

#### Пошук
- `GET /api/v1/search/?q=query&type=all` - пошук по всьому

#### Авторизація
- `POST /api/v1/token/` - отримати JWT токен
- `POST /api/v1/token/refresh/` - оновити токен

---

## 🤖 Telegram Бот

### Функціонал:

#### 1. Inline Search
Користувачі можуть шукати аніме прямо в чатах через `@animex_bot`

#### 2. Прив'язка акаунта
Глибоке посилання для прив'язки Telegram до облікового запису:
```

/start connect_abc123
```
#### 3. Сповіщення
- Нові епізоди улюблених аніме
- Відповіді на коментарі
- Системні повідомлення

#### 4. Команди бота:

```

/start - Запустити бота
/search - Пошук аніме
/mylist - Мій список перегляду
/trending - Трендові аніме
/random - Випадкове аніме
/settings - Налаштування сповіщень
/help - Допомога
```
### Запуск бота:

```
bash
# Встановлення залежностей бота
pip install aiogram==3.4.1

# Запуск
python bot/main.py
```
Або через Docker:
```
bash
docker-compose up bot
```
---

## 📊 Моделі Даних

### Основні моделі:

#### Anime
- Назви (українська, англійська, оригінал)
- Опис, обкладинка, банер
- Жанри, студії
- Тип, статус, кількість епізодів
- Рейтинг, популярність

#### Episode
- Номер епізоду, назва
- Відео файл/URL, HLS потік
- Тривалість, дата виходу
- Перегляди

#### User (кастомний)
- Telegram ID та username
- Досягнення
- Стрік перегляду
- Загальний час перегляду

#### WatchList
- 6 статусів: watching, completed, on_hold, dropped, plan_to_watch, rewatching
- Прогрес епізодів
- Оцінки, нотатки

#### Comment (MPTT)
- Деревоподібна структура
- Реакції, скарги
- Spoiler теги

---

## 🎯 Roadmap

- [ ] Мобільний додаток (React Native)
- [ ] Офлайн перегляд
- [ ] Соціальні функції (друзі, активність)
- [ ] Стрімінг декількох джерел
- [ ] AI рекомендації
- [ ] Мультипрофільність
- [ ] Батьківський контроль

---

## 📝 Ліцензія

Цей проект створено для навчальних цілей.

---

## 👥 Контакти

Для питань та пропозицій:
- Email: your-email@example.com
- Telegram: @your_username

---

## 🙏 Подяки

Дякуємо всім контриб'юторам та користувачам!

---

**Made with ❤️ for anime fans**
```


Цей README детально описує весь ваш проект, структуру, технології та інструкції з використання. Всі шляхи та назви відповідають структурі, яку ви створили!
