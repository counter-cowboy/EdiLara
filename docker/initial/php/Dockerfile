FROM php:8.2-fpm

ENV TZ=Europe/Istanbul PHP_DIR_OWNER=root PHP_DIR_MODE=770

#Install dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    zip \
    unzip \
    vim \
    cron \
    procps

RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
RUN echo gabriel:x:1000:1000:gabriel,,,:/home/gabriel:/bin/bash >> /etc/passwd
RUN apt-get install -f -y nodejs
RUN useradd sail

#Install redis dependency
RUN pecl update-channels && \
    pecl install redis

RUN set -ex; \
    echo "${TZ}" > /etc/timezone; \
    ln -sfn /usr/share/zoneinfo/${TZ} /etc/localtime && echo $TZ > /etc/timezone; \
    printf '[Date]\ndate.timezone="%s"\n', $TZ > /usr/local/etc/php/conf.d/tzone.ini; \
    apt-get update; \
    rm -r /var/lib/apt/lists/*; \
    docker-php-ext-install pdo_mysql bcmath mbstring exif pcntl gd soap zip; \
    apt-get purge -y --auto-remove;

    COPY . /tmp/build/

    RUN set -ex; \
    mkdir -p /var/php/log /var/php/tmp /var/php/sessions /var/php/upload; \
    chown ${PHP_DIR_OWNER}:www-data /var/php/*; \
    chmod ${PHP_DIR_MODE} /var/php/*;

    # Get latest Composer
    COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

    VOLUME ["/var/www", "/var/php"]

    WORKDIR /var/www

    CMD ["php-fpm"]
