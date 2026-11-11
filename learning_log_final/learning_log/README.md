# Learning Log

Учебный проект на **Django 5.2 LTS** (Python 3.12) — сетевой журнал для записи того, что вы узнали по определённой теме.
Выполнен по учебнику «Python & Django» (практика.ркси.рф), раздел «Проект learning_log».

## Возможности

- домашняя страница и список тем;
- страница темы со всеми записями (новые записи — сверху);
- добавление тем и записей, редактирование записей (формы `ModelForm`);
- регистрация, вход и выход пользователей (приложение `users`);
- доступ к страницам только для вошедших пользователей (`@login_required`);
- каждый пользователь видит и меняет только **свои** темы и записи (чужие — страница 404);
- административный сайт Django (`/admin/`).

## Структура проекта

```text
learning_log/              ← корень репозитория (здесь manage.py)
├── manage.py
├── requirements.txt       ← зависимости
├── Dockerfile, compose.yaml, .env.example   ← запуск через Docker Compose
├── learning_log/          ← настройки проекта (settings.py, urls.py, wsgi.py)
├── learning_logs/         ← основное приложение (модели Topic и Entry, формы, представления, шаблоны)
└── users/                 ← регистрация и вход пользователей
```

## Локальный запуск

Нужен Python 3.12.

```bash
# 1. Создать и активировать виртуальное окружение
python3.12 -m venv ll_env            # Windows: py -3.12 -m venv ll_env
source ll_env/bin/activate           # Windows PowerShell: .\ll_env\Scripts\Activate.ps1

# 2. Установить зависимости
python -m pip install -r requirements.txt

# 3. Создать базу данных и администратора
python manage.py migrate
python manage.py createsuperuser

# 4. Запустить сервер разработки
python manage.py runserver
```

Откройте http://127.0.0.1:8000/ — сайт, http://127.0.0.1:8000/admin/ — админка.

> Файл `db.sqlite3` и окружение `ll_env` в репозиторий не входят — они создаются на вашем компьютере.
> Если у вас уже есть база с темами, созданными до добавления владельца темы, при `migrate` Django
> привяжет их к пользователю с id = 1 (обычно это суперпользователь).

## Запуск через Docker Compose

1. Создайте файл `.env` по образцу `.env.example` и задайте свой `DJANGO_SECRET_KEY` и порт `APP_PORT`.
2. Запустите:

```bash
docker compose up -d --build
docker compose ps
docker compose exec web python manage.py createsuperuser
```

Приложение будет доступно по адресу `http://<адрес-сервера>:<APP_PORT>/`.
База SQLite хранится в Docker volume и сохраняется при пересборке. Gunicorn запускается одним рабочим процессом.

### Переменные окружения (`.env`)

| Переменная | Назначение |
| --- | --- |
| `DJANGO_SECRET_KEY` | секретный ключ Django (обязательно задать свой) |
| `DJANGO_DEBUG` | `False` на сервере |
| `DJANGO_ALLOWED_HOSTS` | допустимые адреса через запятую |
| `DJANGO_CSRF_TRUSTED_ORIGINS` | доверенные адреса с портом/схемой, например `http://192.168.1.11:20010` |
| `DJANGO_USE_HTTPS` | `True`, если сайт открыт по HTTPS через домен |
| `SQLITE_PATH` | путь к файлу базы, на сервере `/app/data/db.sqlite3` |
| `APP_BIND_IP` | `0.0.0.0` — приложение доступно снаружи контейнера |
| `APP_PORT` | порт приложения на сервере |

Файл `.env` содержит секреты и **не должен попадать в GitHub**.

## Автор

Никифорова — учебный проект (практика РКСИ).
