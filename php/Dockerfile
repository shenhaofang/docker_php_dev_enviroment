FROM php:7.2.18-fpm-alpine
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
    && apk add --no-cache \
    # for intl extension
    icu-dev \
    # for mcrypt extension
    libmcrypt \
    libmcrypt-dev \
    # libmagickcore-dev \
    # libmagickwand-dev \
    # imagemagick \
    # for databases
    postgresql-dev \
    mysql-client \
#    postgresql-client \
    # for gd extension
    libwebp-dev \
    # libfreetype6-dev \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    # zip extension
    libzip-dev \
    bzip2-dev \
    zip \
    zlib-dev \
    make \
    gettext \
    libevent \
    libevent-dev \
    git \
    libmemcached \
    libmemcached-dev \
    # Lightweight ssh client to ensure low-cost services communications
    # dropbear-ssh \
    # openssh \
    # openssh-server \
    # openrc 
# Install Requirements
RUN set -xe \
 && apk add --no-cache \
    $PHPIZE_DEPS \
#    zlib-dev \
    wget \
    openssh \
    # openssh-server \
    openrc 
RUN php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer
RUN pecl install redis \
    # && pecl install mcrypt\
    && pecl install xdebug \
    # && pecl install nsq \
    && pecl install mongodb \
    # && pecl install imagick \
    && docker-php-ext-enable redis xdebug mongodb
RUN docker-php-ext-configure pdo_pgsql --with-pdo-pgsql \ 
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql \
    && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-configure bz2 --with-bz2 \
    && docker-php-ext-install -j$(nproc) \
    pdo_mysql \
    pdo_pgsql \
    mysqli \
    # mongodb \
    # bcmath \
    zip \
    bz2
RUN git clone https://github.com/php-memcached-dev/php-memcached.git /usr/src/php/ext/memcached \
    && cd /usr/src/php/ext/memcached && git checkout -b php7 origin/php7 \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-configure memcached \
	&& docker-php-ext-install -j$(nproc) gd \
	memcached
# ADD php/php.ini /usr/local/etc/php
ADD php/docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d