FROM php:8.4-fpm-alpine AS app_prod

# Useful PHP extension installer image, copy binary into your container
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/

# Install php extensions
# exit on errors, exit on unset variables, print every command as it is executed
RUN set -eux; \
    install-php-extensions pdo pdo_mysql;

# Install system dependencies for PHP extensions (e.g. gd, intl), and CLI tools (e.g. git, bash)
# RUN apk update && apk add --no-cache \
#     libpng-dev \
#     libjpeg-turbo-dev \
#     freetype-dev \
#     icu-dev \
#     zlib-dev \
#     libxml2-dev \
#     oniguruma-dev \
#     mysql-client \
#     git \
#     unzip \
#     bash \
#     $PHPIZE_DEPS && \
#     docker-php-ext-configure gd --with-freetype --with-jpeg && \
#     docker-php-ext-install gd intl



# allow super user - set this if you use Composer as a
# super user at all times like in docker containers
ENV COMPOSER_ALLOW_SUPERUSER=1

# obtain composer using multi-stage build
# https://docs.docker.com/build/building/multi-stage/
COPY --from=composer:2.8 /usr/bin/composer /usr/bin/composer

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

FROM app_prod AS app_dev

ENV XDEBUG_MODE=off

#Install Xdebug
RUN set -eux; \
    install-php-extensions xdebug; 

# Copy xdebug.ini after xdebug is installed
COPY ./php/conf.d/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
