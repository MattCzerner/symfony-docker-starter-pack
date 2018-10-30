FROM php:7.2-fpm-alpine

# install git for computing diffs
RUN apk add --update git

# install Composer
COPY docker-install-composer /usr/local/bin/docker-install-composer
RUN chmod +x /usr/local/bin/docker-install-composer && docker-install-composer

# libpng-dev needed by "gd" extension
# icu-dev needed by "intl" extension
# postgresql-dev needed by "pgsql" extension
# libzip-dev needed by "zip" extension
# autoconf needed by "redis" extension
# freetype-dev needed by "gd" extension
# libjpeg-turbo-dev needed by "gd" extension
RUN apk add --update \
    libpng-dev \
    icu-dev \
    postgresql-dev \
    libzip-dev \
    autoconf \
    freetype-dev \
    libjpeg-turbo-dev

# install necessary PHP extensions requested by Composer
RUN docker-php-ext-install \
    bcmath \
    gd \
    intl \
    opcache \
    pgsql \
    pdo_pgsql \
    zip

# overwrite the original entry-point from the PHP Docker image with our own
COPY docker-php-entrypoint /usr/local/bin/

# copy php.ini configuration
COPY php-ini-overrides.ini /usr/local/etc/php/php.ini

# copy php.ini configuration
COPY www.conf /usr/local/etc/php-fpm.d/www.conf

RUN deluser www-data && (addgroup -g 1000 www-data || addgroup -g 82 www-data) && adduser -u 1000 -D -S -G www-data www-data
USER www-data

# hirak/prestissimo makes the install of Composer dependencies faster by parallel downloading
RUN composer global require hirak/prestissimo