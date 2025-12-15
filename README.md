# Docker Compose: CRUD «Склады и запасы»

Проект Django `stocks_products` упакован в Docker и разворачивается через Docker Compose с тремя контейнерами: `backend`, `postgres`, `nginx`.

## Состав
- backend: Django + Gunicorn (`stocks_products.wsgi:application`) на порту `8000`
- postgres: PostgreSQL 16 (alpine)
- nginx: reverse proxy, отдаёт `/static/` и проксирует динамику на backend

## Требования
- Установлены Docker и Docker Compose

## Быстрый старт
```bash
docker compose up -d --build
```
После старта:
- Миграции и сборка статики выполняются автоматически в контейнере `backend`
- Приложение доступно на `http://localhost/`

## Окружение
Переменные окружения для `backend` задаются в `docker-compose.yml`:
- `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_HOST`, `POSTGRES_PORT`
- `DJANGO_SECRET_KEY` — замените на свой ключ
- `DJANGO_DEBUG` — `"False"` для продакшна
- `DJANGO_ALLOWED_HOSTS` — список хостов через запятую

В `settings.py` переключение на PostgreSQL происходит автоматически при наличии `POSTGRES_DB` (stocks_products/stocks_products/settings.py:54).
Статика собирается в `STATIC_ROOT` (`/app/static`) и монтируется в `nginx` (см. docker-compose.yml).

## Полезные команды
- Логи backend: `docker compose logs -f backend`
- Логи nginx: `docker compose logs -f nginx`
- Логи postgres: `docker compose logs -f postgres`
- Остановить: `docker compose down`
- Пересобрать: `docker compose up -d --build`

## Архитектура
- `stocks_products/Dockerfile` — образ backend (Django+Gunicorn), выполняет `collectstatic`, `migrate`, запускает Gunicorn
- `nginx/nginx.conf` — конфиг Nginx, проксирование на `backend:8000`, отдача `/static/`
- `docker-compose.yml` — схема сервисов, тома и сеть
- `stocks_products/requirements.txt` — зависимости (включая `gunicorn`, `psycopg2-binary`)

## Разработка
Если требуется включить DEBUG:
```yaml
environment:
  DJANGO_DEBUG: "True"
  DJANGO_ALLOWED_HOSTS: "localhost,127.0.0.1"
```

## Публикация в GitHub
Репозиторий: `git@github.com:Edo19999/Docker-Compose.git`
```bash
git init
git remote add origin git@github.com:Edo19999/Docker-Compose.git
git add .
git commit -m "Docker Compose: backend+postgres+nginx для stocks_products"
git push -u origin main
```
Если ветка `main` отсутствует:
```bash
git branch -M main
git push -u origin main
```

