FROM php:8.4-fpm-alpine

# Install PHP extensions and dependencies
RUN apk update && apk upgrade --no-cache && \
    apk add --no-cache \
        libpq \
        libpng \
        libjpeg-turbo \
        freetype \
        icu-libs \
        zlib \
        libxml2 \
        oniguruma \
        mysql-client \
        git \
        unzip \
        bash \
        icu && \
    docker-php-ext-install pdo pdo_mysql

# Optional: Clean up to reduce image size (not strictly necessary with --no-cache)
RUN rm -rf /var/cache/apk/*

# allow super user - set this if you use Composer as a
# super user at all times like in docker containers
ENV COMPOSER_ALLOW_SUPERUSER=1

# obtain composer using multi-stage build
# https://docs.docker.com/build/building/multi-stage/
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

#Here, we are copying only composer.json and composer.lock (instead of copying the entire source)
# right before doing composer install.
# This is enough to take advantage of docker cache and composer install will
# be executed only when composer.json or composer.lock have indeed changed!-
# https://medium.com/@softius/faster-docker-builds-with-composer-install-b4d2b15d0fff
COPY ./app/composer.* ./

# install
RUN composer install --prefer-dist --no-dev --no-scripts --no-progress --no-interaction

# copy application files to the working directory
COPY ./app .

# run composer dump-autoload --optimize
RUN composer dump-autoload --optimize