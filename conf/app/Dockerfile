FROM php:8.1-fpm-alpine3.16

ARG TZ

# Установка timezone
RUN apk add --no-cache tzdata \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo "${TZ}" > /etc/timezone \
    && apk del tzdata

# Установка пакетов
RUN apk update

RUN apk add --no-cache libzip icu imap c-client tidyhtml freetype libpng libjpeg-turbo ncurses build-base git nodejs npm yarn openssh libcurl curl-dev openssl openssl-dev nano go bash

RUN apk add --update --no-cache --virtual .build-ext-deps libzip-dev icu-dev imap-dev openssl-dev tidyhtml-dev freetype-dev libjpeg-turbo-dev libpng-dev  \
    && docker-php-ext-configure zip \
    && docker-php-ext-configure imap --with-imap --with-imap-ssl \
    && docker-php-ext-configure opcache --enable-opcache \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) pdo_mysql zip intl imap tidy pcntl opcache bcmath gd exif sockets \
    && apk del .build-ext-deps 

# Установка python3
ENV PYTHONUNBUFFERED=1
RUN apk add --update --no-cache python3 \
    && ln -sf python3 /usr/bin/python \
    && python3 -m ensurepip \
    && pip3 install --no-cache --upgrade pip setuptools

# Установка npm пакетов
RUN npm install --global expo-cli chokidar

# Установка pecl пакетов
RUN apk add --no-cache libmemcached \
    && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS libmemcached-dev zlib-dev \
    && pecl install redis memcached xdebug mongodb \
    && docker-php-ext-enable redis memcached xdebug mongodb \
    && rm -rf /tmp/pear \
    && apk del -f .build-deps

# Установка конфигурации
RUN cp "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

# Установка composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Устаровка пользователя
USER www-data

# Порты
EXPOSE 9000 8000

# Старт php-fpm
CMD ["php-fpm"]