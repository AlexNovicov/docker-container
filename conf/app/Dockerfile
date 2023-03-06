FROM php:8.1-fpm-alpine3.16

ARG TZ

# Установка timezone
RUN set -eux \
    && apk add --no-cache tzdata \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo "${TZ}" > /etc/timezone \
    && apk del tzdata

# Установка пакетов
RUN set -eux \
    && apk add --no-cache \
        bash \
        autoconf \
        openssh \
        openssl \
        icu \
        imap \
        nmap \
        linux-headers \
        c-client \
        ca-certificates \
        tidyhtml \
        freetype \
        gettext \
        gmp \
        icu-libs \
        libzip \
        libpng \
        libjpeg-turbo \
        libgmpxx \
        libcurl \
        libpq \
        libmemcached \
        imagemagick \
        libffi \
        libintl \
        libssh2 \
        libstdc++ \
        libwebp \
        ncurses \
        build-base \
        procps \
        bind-tools \
        make \
        nano \
        vim \
        git \
        nodejs \
        npm \
        yarn \
        go

# Установка пакетов
RUN set -eux \
    && apk add --no-cache --virtual .build-deps-1 \
        \
        # Dev пакеты
        libzip-dev \
        libjpeg-turbo-dev \
        libpng-dev  \
        libpq-dev \
        curl-dev \
        gettext-dev \
        icu-dev \
        imap-dev \
        openssl-dev \
        tidyhtml-dev \
        freetype-dev \
        libxml2-dev \
        libwebp-dev \
        imagemagick-dev \
        pcre-dev \
        gmp-dev \
        zlib-dev \
        libmemcached-dev \
        libssh2-dev \
        \
        # PHP пакеты
        && docker-php-ext-configure zip \
        && docker-php-ext-configure imap \
            --with-imap \
            --with-imap-ssl \
        && docker-php-ext-configure opcache \
            --enable-opcache \
        && docker-php-ext-configure gd \
            --enable-gd \
            --with-webp \
            --with-jpeg \
            --with-freetype \
            --enable-gd-jis-conv \
        && docker-php-ext-install -j$(nproc) \
            zip \
            intl \
            imap \
            tidy \
            pcntl \
            opcache \
            bcmath \
            gd \
            exif \
            sockets \
            gettext \
            soap \
            gmp \
            bz2 \
            pdo_mysql \
            pdo_pgsql \
            pgsql \
            pdo \
            mysqli \
        && apk del .build-deps-1

# Установка pecl пакетов
RUN set -eux \
    && apk add --no-cache --virtual .build-deps-2 \
        $PHPIZE_DEPS \
        openssl-dev \
        zlib-dev \
    && pecl install \
        redis \
        xdebug \
        mongodb \
        protobuf \
        grpc \
    && docker-php-ext-enable \
        redis \
        xdebug \
        mongodb \
        protobuf \
        grpc \
    && rm -rf /tmp/pear \
    && apk del -f .build-deps-2

# Установка python3
ENV PYTHONUNBUFFERED=1
RUN set -eux \
    && apk add --no-cache python3 \
    && ln -sf python3 /usr/bin/python \
    && python3 -m ensurepip \
    && pip3 install --no-cache --upgrade \
        pip \
        setuptools

# Установка npm пакетов
RUN npm install --global \
    expo-cli \
    chokidar

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