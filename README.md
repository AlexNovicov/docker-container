# Profilance Group server docker contaner


## **1. Установка Docker**

1. Для работы требуется [установить докер](https://www.docker.com/) на уровне операционной системы ([для MacBook M1](https://docs.docker.com/docker-for-mac/apple-m1/)).
2. Клонировать данный репозиторий. Рекомендуется клонировать в папку с определенным называние, так как она будет использоваться в качстве названия контейнера и префиксов. В примере ниже клонирование репозитория в папку profilancegroup.

`git clone git@gitlab.profilancegroup-tech.com:tools/docker-server.git profilancegroup`

## **2. Создание .env**

Нужно перейти в дирректориию клонированного репозитория и выполнить команду:

`cp .env.example .env`

## **3. Сборка образа (Image)**

Находясь в директории клонированного репозитория выполнить команду:

`docker-compose build`

Данная команда соберет образ. При обновлении docker конфигурации нужно будет заново провести сборку образа.

## **4. Запуск**

Для запуска контейнра нужно находиться в дирректориии клонированного репозитория и выполнить команду:

`docker-compose up -d` 

Опция -d позволяет запустить контейнер в фоновом режиме.

## **5. Настройка хостов**

Содержимое файла conf/hosts нужно добавить в файл хостов на своей локалке /etc/hosts

## **6. Первичная настройка базы данных**

При первичной настройке создается пользователь profilancegroup с паролем 12345678. Также создается базовый набор баз данных.

1. Подключение по ssh в контейнер с mariadb:

    `docker exec -it pg-mariadb sh`

2. Выполнение заготовленного sql запроса:

    `mysql -u root -p12345678 < /etc/mysql/pg-init.sql`

3. Завершение ssh сессии:

    `exit`

Для доступа в консоль mysql:

`mysql -u root -p12345678`

## **7. Софт**

### **7.1. phpMyAdmin**

Адрес клиента: http://localhost:8010/


```
Сервер: pg-mariadb
Пользователь: profilancegroup
Пароль: 12345678
```

### **7.1. Mongo Express**

Адрес клиента: http://localhost:8011/

### **7.1. PHPStorm**

В PHPStorm проекты открывать из директории www этого репозитория и работать как обычно. Можно перенести из других мест проекты, чтобы они работали в контейнере, либо скопировать их, либо клонировать заново.

## **8. Установка и настройка проектов**

Подключение к контейнеру с проектами по ssh:

`docker exec -it pg-app sh '-l'` 

Для удобства можно добавить алиас:

`alias pg-docker-app="docker exec -it pg-app sh '-l'"`

Сразу же после логина по ssh по умолчанию будет открыта /var/www, Данная директория шарится с локальной директорией www из данного репозитория.
В директории /var/www должны находиться все проекты.

В контейнере установлен весь необходимый софт для работы. Так же в контейнере уже пошарены ваши ssh ключи и gitconfig.

Инструкция по установке каждого проекта находится в readme.md самого проекта.