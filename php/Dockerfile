FROM php:fpm-alpine
ENV PHPIZE_DEPS \
    autoconf \
    cmake \
    file \
    g++ \
    gcc \
    libc-dev \
    pcre-dev \
    pkgconf \
    re2c
RUN apk update
RUN sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" /etc/apk/repositories \
    && apk add --no-cache --virtual .persistent-deps \
    # for intl extension
    icu-dev \
    # for mcrypt extension
    libmcrypt-dev \
    # for databases
    postgresql-dev \
    mysql-client \
#    postgresql-client \
    # for gd extension
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    # zip extension
    libzip-dev \
    zip \
    make \
    gettext \
    libevent \
    libevent-dev \
    git \
    # Lightweight ssh client to ensure low-cost services communications
    dropbear-ssh

# Install Requirements
RUN set -xe \
 && apk add --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
#    zlib-dev \
    wget
RUN pecl install redis \
    && pecl install xdebug \
    && pecl install nsq \
    #&& pecl install mysqli \
    && docker-php-ext-enable redis xdebug nsq
RUN docker-php-ext-configure pdo_pgsql --with-pdo-pgsql \ 
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql \
    && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
    && docker-php-ext-install -j$(nproc) \
    pdo_mysql \
    pdo_pgsql \
    mysqli
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
	&& docker-php-ext-install -j$(nproc) gd
ADD php/php.ini /usr/local/etc/php
ADD php/docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d
ADD nginx/vhosts/ /etc/nginx/conf.d/vhosts