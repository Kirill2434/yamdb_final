# yamdb_final
_**REST API для сервиса YaMDb**_

Проект YaMDb собирает отзывы пользователей на произведения.
Произведения делятся на категории: «Книги», «Фильмы», «Музыка» и т.д., так же им может быть присвоен жанр.

Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы и ставят произведению оценку в диапазоне от одного до десяти; из пользовательских оценок формируется рейтинг. На одно произведение пользователь может оставить только один отзыв.

Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.

## Как запустить проект: 
Все описанное ниже относится к ОС Linux. 
Клонируем репозиторий и и переходим в него: 
```bash 
git clone git@github.com:Kirill2434/yamdb_final.git
cd yamdb_final 
cd api_yamdb 
``` 
 
Создаем и активируем виртуальное окружение: 
```bash 
python3 -m venv venv 
source /venv/Scripts/activate
python -m pip install --upgrade pip 
``` 
 
Ставим зависимости из requirements.txt: 
```bash 
pip install -r requirements.txt 
``` 

Переходим в папку с файлом docker-compose.yaml: 
```bash 
cd infra 
``` 
 
Поднимаем контейнеры (infra_db_1, infra_web_1, infra_nginx_1): 
```bash 
docker-compose up -d --build 
``` 

Выполняем миграции: 
```bash 
docker-compose exec web python manage.py makemigrations reviews 
``` 
```bash 
docker-compose exec web python manage.py migrate --run-syncdb
``` 

Создаем суперпользователя: 
```bash 
docker-compose exec web python manage.py createsuperuser 
``` 

Србираем статику: 
```bash 
docker-compose exec web python manage.py collectstatic --no-input 
``` 

Создаем дамп базы данных (нет в текущем репозитории): 
```bash 
docker-compose exec web python manage.py dumpdata > dumpPostrgeSQL.json 
``` 

Останавливаем контейнеры: 
```bash 
docker-compose down -v 
```

_**К проекту по адресу http://IP_адрес/redoc/ подключена документация API YaMDb. В ней описаны возможные запросы к API и структура ожидаемых ответов.**_


## Регистрация новых пользователей

- Пользователь отправляет POST-запрос с параметрами email и username на эндпоинт /api/v1/auth/signup/.
- Сервис YaMDB отправляет письмо с кодом подтверждения на указанный адрес email.
- Пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт /api/v1/auth/token/, в ответе на запрос ему приходит token (JWT-токен).
В результате пользователь получает токен и может работать с API проекта, отправляя этот токен с каждым запросом.
- После регистрации и получения токена пользователь может отправить PATCH-запрос на эндпоинт /api/v1/users/me/ и заполнить поля в своём профайле (описание полей — в документации).

## Ресурсы API YaMDb

- auth: аутентификация.
- users: пользователи.
- titles: произведения, к которым пишут отзывы (определённый фильм, книга или песенка).
- categories: категории произведений («Фильмы», «Книги», «Музыка»).
- genres: жанры произведений. Одно произведение может быть привязано к нескольким жанрам.
- reviews: отзывы на произведения. Отзыв привязан к определённому произведению.
- comments: комментарии к отзывам. Комментарий привязан к определённому отзыву.

Каждый ресурс описан в документации: указаны эндпоинты (адреса, по которым можно сделать запрос), разрешённые типы запросов, права доступа и дополнительные параметры, если это необходимо.

ID сервера: 178.154.220.62

[![Django-app workflow](https://github.com/Kirill2434/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)](https://github.com/Kirill2434/yamdb_final/actions/workflows/yamdb_workflow.yml)