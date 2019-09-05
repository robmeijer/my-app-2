FROM php:7.3-fpm-alpine

# Use the default production configuration
RUN cp "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

COPY docker/php/conf.d/default.ini $PHP_INI_DIR/conf.d/default.ini

# Install dependencies
RUN apk --update add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        icu-dev \
        libzip-dev \
        zlib-dev \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-install \
        intl \
        pcntl \
        pdo_mysql \
        zip \
    && pecl -q install \
        mongodb \
    && pecl clear-cache \
    && docker-php-ext-enable \
        mongodb \
        opcache \
    && runDeps="$( \
        scanelf --needed --nobanner --format '%n#p' --recursive /usr/local/lib/php/extensions \
            | tr ',' '\n' \
            | sort -u \
            | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
    )" \
    && apk add --no-cache --virtual .run-deps $runDeps \
    && apk del .build-deps

CMD ["php-fpm"]
