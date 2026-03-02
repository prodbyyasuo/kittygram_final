# Kittygram

**Kittygram** — это веб-приложение для ведения учета домашних питомцев (кошек). Пользователи могут регистрироваться, добавлять информацию о своих кошках, загружать фотографии и отслеживать достижения питомцев.

## О проекте

Kittygram — это финальный проект спринта, демонстрирующий навыки контейнеризации, настройки CI/CD и развёртывания полнофункционального веб-приложения.

### Цели проекта

- Создание удобного интерфейса для учёта домашних питомцев
- Возможность загрузки и хранения фотографий
- Система достижений для кошек
- Контейнеризация приложения для упрощения развёртывания
- Настройка автоматического тестирования и деплоя

### Основные функции

- **Регистрация и аутентификация пользователей** — создание учётной записи, вход по токену
- **Управление котиками** — создание, редактирование, удаление записей о питомцах
- **Загрузка фотографий** — добавление изображений для каждого кота
- **Достижения** — отслеживание достижений питомцев
- **Админ-панель** — управление пользователями и контентом через Django Admin

---

## Стек технологий

### Backend
- **Python 3.10** — язык программирования
- **Django 4.2** — веб-фреймворк
- **Django REST Framework 3.15** — создание API
- **Djoser 2.3** — аутентификация и регистрация
- **PostgreSQL 13** — база данных
- **Gunicorn 20.1** — WSGI-сервер
- **Pillow 11.1** — работа с изображениями

### Frontend
- **Node.js 18** — среда выполнения JavaScript
- **React 18** — библиотека для создания интерфейса
- **npm** — менеджер пакетов

### Инфраструктура
- **Docker & Docker Compose** — контейнеризация
- **Nginx 1.22** — веб-сервер и обратный прокси
- **GitHub Actions** — CI/CD автоматизация
- **Docker Hub** — хостинг Docker-образов

---

## Развёртывание проекта

### Требования

- Docker и Docker Compose
- Git

### 1. Клонирование репозитория

```bash
git clone https://github.com/prodbyyasuo/kittygram_final.git
cd kittygram_final
```

### 2. Настройка переменных окружения

Создайте файл `.env` в корневой директории проекта:

```bash
cp .env.example .env
```

Отредактируйте файл `.env` и заполните переменными:

```env
# PostgreSQL
POSTGRES_DB=kittygram
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=kittygram_password
DB_NAME=kittygram
DB_HOST=db
DB_PORT=5432

# Django
SECRET_KEY=ваш-секретный-ключ
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Для продакшена укажите реальный домен
# DEBUG=False
# ALLOWED_HOSTS=ваш-домен.ru
```

### 3. Запуск контейнеров

```bash
docker-compose up --build -d
```

Проверьте статус контейнеров:

```bash
docker-compose ps
```

Должны быть запущены:
- `db` — PostgreSQL
- `backend` — Django API
- `frontend` — React приложение
- `gateway` — Nginx

### 4. Применение миграций

```bash
docker-compose exec backend python manage.py migrate
```

### 5. Создание суперпользователя

```bash
docker-compose exec backend python manage.py createsuperuser
```

### 6. Доступ к приложению

- **Фронтенд**: http://localhost:9000/
- **API**: http://localhost:9000/api/
- **Админ-панель**: http://localhost:9000/admin/

---

## Структура проекта

```
kittygram_final/
├── backend/                 # Django бэкенд
│   ├── cats/               # Приложение котов
│   ├── kittygram_backend/  # Настройки проекта
│   ├── manage.py
│   ├── requirements.txt
│   └── Dockerfile
├── frontend/               # React фронтенд
│   ├── public/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
├── nginx/                  # Конфигурация Nginx
│   ├── nginx.conf
│   └── Dockerfile
├── tests/                  # Автотесты
│   ├── test_connection.py
│   ├── test_files.py
│   └── conftest.py
├── .github/workflows/      # GitHub Actions
│   └── main.yml
├── docker-compose.yml      # Конфигурация Docker Compose
├── docker-compose.production.yml
├── .env                    # Переменные окружения
├── .env.example
└── README.md
```

---

## CI/CD с GitHub Actions

При пуше в ветку `main` автоматически:

1. Запускаются тесты для бэкенда и фронтенда
2. Проверяется код через `ruff`
3. Собираются Docker-образы
4. Образы загружаются на Docker Hub:
   - `yasuo2k/kittygram_backend:latest`
   - `yasuo2k/kittygram_frontend:latest`
   - `yasuo2k/kittygram_gateway:latest`
5. Отправляется уведомление в Telegram

### Требуемые секреты GitHub

В настройках репозитория (Settings → Secrets and variables → Actions) добавьте:

| Секрет | Описание |
|--------|----------|
| `DOCKER_USERNAME` | Логин на Docker Hub |
| `DOCKER_PASSWORD` | Пароль или токен Docker Hub |
| `TELEGRAM_TOKEN` | Токен Telegram-бота |
| `TELEGRAM_TO` | ID чата для уведомлений |

---

## Развёртывание на сервере (VPS)

### 1. Подготовка сервера

Установите Docker и Docker Compose на сервер.

### 2. Клонирование проекта

```bash
git clone https://github.com/prodbyyasuo/kittygram_final.git
cd kittygram_final
```

### 3. Настройка .env

Создайте `.env` с продакшен-настройками:

```env
DEBUG=False
ALLOWED_HOSTS=ваш-домен.ru
SECRET_KEY=надёжный-секретный-ключ
POSTGRES_DB=kittygram
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=надёжный-пароль
DB_HOST=db
DB_PORT=5432
```

### 4. Запуск в продакшене

```bash
docker-compose -f docker-compose.production.yml up -d
```

### 5. Настройка Nginx на сервере

Создайте конфигурацию для маршрутизации между Taski и Kittygram:

```nginx
server {
    listen 80;
    server_name ваш-домен.ru;

    location /kittygram/ {
        proxy_pass http://localhost:9000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location /taski/ {
        proxy_pass http://localhost:8000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### 6. Настройка HTTPS (рекомендуется)

Используйте Let's Encrypt для получения SSL-сертификата:

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d ваш-домен.ru
```

---

## Тестирование

### Локальный запуск тестов

```bash
# Установите зависимости
pip install -r backend/requirements.txt

# Запустите pytest
pytest
```

### Запуск отдельных тестов

```bash
# Только тесты файлов
pytest tests/test_files.py -v

# Только тесты соединения (требуется доступ к проекту)
pytest tests/test_connection.py -v
```

### Файл tests.yml

Для тестов соединения создайте `tests.yml` в корне проекта:

```yaml
repo_owner: prodbyyasuo
kittygram_domain: https://ваш-домен.ru/kittygram/
taski_domain: https://ваш-домен.ru/taski/
dockerhub_username: yasuo2k
```

---

## Локальное тестирование через туннель

Для прохождения тестов `test_connection.py` без развёртывания на VPS можно использовать туннели.

### Вариант 1: Cloudflare Tunnel (рекомендуется)

**1. Установи cloudflared:**

```bash
# Windows (через chocolatey)
choco install cloudflared

# macOS
brew install cloudflared

# Linux
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared.deb
```

**2. Запусти проект локально:**

```bash
docker-compose up --build -d
```

**3. Запусти туннели в двух терминалах:**

```bash
# Терминал 1 - Taski (порт 8000)
cloudflared tunnel --url http://localhost:8000

# Терминал 2 - Kittygram (порт 9000)
cloudflared tunnel --url http://localhost:9000
```

**4. Скопируй ссылки из вывода:**

```
https://xxxx-xxxx-xxxx.trycloudflare.com
```

**5. Обнови `tests.yml`:**

```yaml
repo_owner: prodbyyasuo
kittygram_domain: https://yyyy-yyyy-yyyy.trycloudflare.com
taski_domain: https://xxxx-xxxx-xxxx.trycloudflare.com
dockerhub_username: yasuo2k
```

**6. Запусти тесты:**

```bash
pytest tests/test_connection.py -v
```

---

### Вариант 2: localtunnel

**1. Установи localtunnel:**

```bash
npm install -g localtunnel
```

**2. Запусти проект:**

```bash
docker-compose up --build -d
```

**3. Запусти туннели в двух терминалах:**

```bash
# Терминал 1 - Taski
lt --port 8000

# Терминал 2 - Kittygram
lt --port 9000
```

**4. Открой ссылки в браузере и прими предупреждение:**

```
https://xxxx-xxxx.loca.lt
```

⚠️ **Важно:** При первом открытии locta.lt показывает страницу предупреждения. Нужно нажать "Proceed" или ввести пароль (если отображается в терминале).

**5. Обнови `tests.yml`:**

```yaml
repo_owner: prodbyyasuo
kittygram_domain: https://yyyy-yyyy.loca.lt
taski_domain: https://xxxx-xxxx.loca.lt
dockerhub_username: yasuo2k
```

**6. Обнови `.env` для разрешения всех хостов:**

```env
ALLOWED_HOSTS=*
```

**7. Пересобери контейнер:**

```bash
docker-compose down
docker-compose up --build -d
```

**8. Запусти тесты:**

```bash
pytest tests/test_connection.py -v
```

---

### Вариант 3: localhost.run (через SSH)

```bash
# Терминал 1 - Taski
ssh -R 80:localhost:8000 ssh.localhost.run

# Терминал 2 - Kittygram
ssh -R 80:localhost:9000 ssh.localhost.run
```

**Скопируй ссылки** вида `https://xxxx-xxxx.localhost.run` и вставь в `tests.yml`.

---

## API Endpoints

| Метод | URL | Описание |
|-------|-----|----------|
| POST | `/api/users/` | Регистрация пользователя |
| POST | `/api/token/login/` | Получение токена |
| POST | `/api/token/logout/` | Выход из системы |
| GET | `/api/cats/` | Список котов |
| POST | `/api/cats/` | Создание кота |
| GET | `/api/cats/{id}/` | Детали кота |
| PUT | `/api/cats/{id}/` | Обновление кота |
| DELETE | `/api/cats/{id}/` | Удаление кота |
| GET | `/api/achievements/` | Список достижений |

---

## Авторы

- **Разработчик**: [prodbyyasuo](https://github.com/prodbyyasuo)

---

## Лицензия

Проект создан в учебных целях.
