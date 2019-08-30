FROM php:7.3-fpm-alpine

# Use the default production configuration
RUN cp "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

COPY docker/php/conf.d/default.ini $PHP_INI_DIR/conf.d/default.ini

# Install dependencies
RUN set -xe \
    && apk --update add --no-cache icu-dev \
    && docker-php-ext-install pdo pdo_mysql intl pcntl \
    && docker-php-ext-enable opcache

CMD ["php-fpm"]
