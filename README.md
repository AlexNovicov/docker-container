# ProfilanceGroup server docker contaner


## **1. Установка Docker**

1. Для работы требуется [установить докер](https://www.docker.com/) на уровне операционной системы ([для MacBook M1](https://docs.docker.com/docker-for-mac/apple-m1/)).
2. Клонировать данный репозиторий. В примере ниже клонирование репозитория в папку profilancegroup.

```
git clone git@gitlab.profilancegroup-tech.com:tools/docker-server.git profilancegroup
```

## **2. Настройка .env**

Нужно перейти в дирректориию клонированного репозитория и выполнить команду:

```
cp .env.example .env
```

## **3. Сборка образов и запуск контейнеров**

Находясь в директории клонированного репозитория выполнить команду:

```
docker-compose up -d --build
```

Данная команда соберет образы и запустит контейнеры.

Опция -d позволяет запустить контейнер в фоновом режиме.

Опция --build делает сборку перез запуском.

При обновлении docker конфигурации нужно будет заново провести сборку образа, для этого нужно выполнить:


```
docker-compose down
docker-compose up -d --build
```


## **4. Настройка хостов**

2 варианта на выбор:

- Содержимое файла hosts нужно добавить в файл хостов на своей локалке /etc/hosts

- Поставить на уровне системы Dnsmasq и с его помощью для всех доменов .loc определить адрес 127.0.0.1 и вообще никогда не трогать /etc/hosts. Вариант самый удобный и избавляет от необходимости постоянно добавлять домены в /etc/hosts.

## **5. Первичная настройка базы данных**

При первичной настройке создается пользователь profilancegroup с паролем 12345678. Также создается базовый набор баз данных.

1. Подключение по ssh в контейнер с mariadb:

```
docker exec -it pg-mariadb sh
```

2. Выполнение заготовленного sql запроса:

```
mysql -u root -p12345678 < /etc/mysql/sql/pg-init.sql
```

3. Завершение ssh сессии:

```
exit
```

Для доступа в консоль mysql:

```
mysql -u root -p12345678`
```

## **6. Софт**

### **6.1. phpMyAdmin**

http://localhost:8010/


```
Пользователь: root
Пароль: 12345678
```

### **6.2. Mongo Express**

http://localhost:8011/

```
Пользователь: root
Пароль: 12345678
```

### **6.3. Mailpit**

http://localhost:8012/

### **6.4. PHPStorm**

В PHPStorm проекты открывать из директории www этого репозитория и работать как обычно. Можно перенести из других мест проекты, чтобы они работали в контейнере, либо скопировать их, либо клонировать заново.

## **7. Установка и настройка проектов**

Подключение к контейнеру с проектами по ssh:

```
docker exec -it pg-app sh '-l'
``` 

Для удобства можно добавить алиас:


```
lias pg-docker-app="docker exec -it pg-app sh '-l'"
```

Сразу же после логина по ssh по умолчанию будет открыта /var/www, Данная директория шарится с локальной директорией www из данного репозитория.
В директории /var/www должны находиться все проекты.

В контейнере установлен весь необходимый софт для работы. Так же в контейнере уже пошарены ваши ssh ключи и gitconfig.

Инструкция по установке каждого проекта находится в readme.md самого проекта.