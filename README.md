# Taski
![Django](https://a11ybadges.com/badge?logo=django)
![Python](https://a11ybadges.com/badge?logo=python)
![SQLite](https://img.shields.io/badge/sqlite-%2307405e.svg?style=for-the-badge&logo=sqlite&logoColor=white)
![Docker](https://a11ybadges.com/badge?logo=docker)
![GitHub Actions](https://a11ybadges.com/badge?logo=githubactions)  


Taski - это записная книжка для дел на день. Пользователь может записывать свои дела и помечать, если они были выполнены.
Настроены  CI/CD проекта на сервер.

### _Развернуть проект на удаленном сервере:_

**_Клонировать репозиторий:_**
```
git@github.com:TatianaSharova/taski-docker.git
```
**_Установить на сервере Docker, Docker Compose:_**
```
sudo apt install curl                                   - установка утилиты для скачивания файлов
curl -fsSL https://get.docker.com -o get-docker.sh      - скачать скрипт для установки
sh get-docker.sh                                        - запуск скрипта
sudo apt-get install docker-compose-plugin              - последняя версия docker compose
```
**_Скопировать на сервер в папку taski файл docker-compose.production.yml (команду выполнять, находясь в папке проекта):_**
```
scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/taski/docker-compose.production.yml

# SSH_name — имя файла с SSH-ключом (без расширения)
# path_to_SSH — путь к файлу с SSH-ключом
# username - имя пользователя на сервере
# server_ip — IP вашего сервера
```

**_Для работы с GitHub Actions необходимо в репозитории в разделе Secrets > Actions создать переменные окружения:_**
```
DOCKER_PASSWORD         - пароль от Docker Hub
DOCKER_USERNAME         - логин Docker Hub
HOST                    - публичный IP сервера
USER                    - имя пользователя на сервере
SSH_KEY                 - приватный ssh-ключ
SSH_PASSPHRASE          - пароль для ssh-ключа
TELEGRAM_TO             - ID телеграм-аккаунта для посылки сообщения
TELEGRAM_TOKEN          - токен бота, посылающего сообщение
```
**_На сервере в папке taski создать файл .env и внести туда следующие данные:_**
```
POSTGRES_DB             - имя бд
POSTGRES_USER           - имя пользователя бд
POSTGRES_PASSWORD       - пароль от бд
DB_HOST                 - db
DB_PORT                 - 5432
```
**_Создание Docker-образов:_**

1.  Замените username на ваш логин на DockerHub:

    ```
    Из папки frontend выполнить команду:
    docker build -t username/taski_frontend .

    Из папки backend выполнить команду:
    docker build -t username/taski_backend .

    Из папки nginx выполнить команду:
    docker build -t username/taski_gateway . 
    ```

2. Загрузите образы на DockerHub:

    ```
    docker push username/taski_frontend
    docker push username/taski_backend
    docker push username/taski_gateway
    ```

**_Запустить контейнеры Docker:_**
```
sudo docker compose -f docker-compose.production.yml up -d
```
**_Выполнить миграции:_**
```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
```
**_Собрать статику:_**
```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic

sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```
**_Создать суперпользователя:_**
```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py createsuperuser
```
**_Для остановки контейнеров Docker:_**
```
sudo docker compose -f docker-compose.production.yml down -v      - с удалением контейнеров и томов
sudo docker compose -f docker-compose.production.yml stop         - без удаления
```
### После каждого обновления репозитория (push в ветку master) будет происходить:

1. Сборка и доставка докер-образов frontend, backend, gateway на Docker Hub
2. Разворачивание проекта на удаленном сервере
3. Отправка сообщения в Telegram в случае успеха

### Локальный запуск проекта:

**_Склонировать репозиторий к себе_**
```
git@github.com:TatianaSharova/taski-docker.git
```

**_В директории проекта создать файл .env и заполнить своими данными:_**
```
POSTGRES_USER
POSTGRES_PASSWORD
POSTGRES_DB
DB_HOST=db
DB_PORT=5432

```

**_В файле settings.py подключить default local database:_**
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'db.sqlite3', }}

```

**_Запустить контейнеры Docker из папки с docker-compose.yml:._**

```
docker compose up
```
```
docker compose exec backend python manage.py migrate
```
```
docker compose exec backend python manage.py createsuperuser
```
После запуска проект будут доступен по адресу: http://localhost:8000/

Админка проекта будет доступна по адресу: http://localhost:8000/admin/

### Автор
[Татьяна Шарова](https://github.com/TatianaSharova)

