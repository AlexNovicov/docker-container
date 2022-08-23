# Profilance group server docker contaner

Первоисточник - https://github.com/vgocoder/docker-compose-laravel-alpine-for-mac-m1

## **Предусловия**

Для работы требуется [установить докер]((https://docs.docker.com/docker-for-mac/install/) на уровне операционной системы и клонировать данный репозиторий.

Для Mac M1 докер устанавливать [отсюда](https://docs.docker.com/docker-for-mac/apple-m1/)

## **Быстрый запуск**

### Первичная сборка контейнеров

В терминале перейдите в директокторию клонированного репозитория и выполните команду:

- `docker-compose up -d --build`

При повторном запуске контейнеров достаточно выполнить

- `docker-compose up -d` 

### Определение хостов

По умолчанию все проекты настроены на локальный хост вида guldog.loc. Для корректной работы в /etc/hosts добавить:
`
127.0.0.1       vsesdal.loc
127.0.0.1       guldog.loc
127.0.0.1       murchalkin.loc
127.0.0.1       nyanyaryadom.loc
`

### Первичная настройка базы данных

При первичной настройке создается пользователь test с паролем 12345678. Также создается базовый набор баз данных.

- `docker exec -it pg-mariadb sh` 
- `mysql -u root -p12345678 < /etc/mysql/pg-init.sql`
- `exit`

Для доступа в консоль mysql выполнить:
- `mysql -u root -p12345678 < /etc/mysql/pg-init.sql`

### Установка проекта

Для открытия консоли основного контейнера с php и нужными пакетами:
- `docker exec -it pg-app sh` 

Дальше как обычно проводим клонирование нужного репозитория, установку пакетов, применение миграций и сборку фронта при необходимости. 

Данные .env для подключения к базам данных:
`
DB_CONNECTION=mysql
DB_HOST=pg-mariadb
DB_PORT=3306
DB_DATABASE=guldog
DB_USERNAME=test
DB_PASSWORD=12345678
MONGODB_HOST=pg-mongo
MONGODB_USERNAME=root
MONGODB_PASSWORD=12345678
REDIS_HOST=pg-redis
`