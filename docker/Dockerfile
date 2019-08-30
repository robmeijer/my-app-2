FROM php:7.3-fpm-alpine as base

# Use the default production configuration
RUN cp "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

COPY docker/php/conf.d/default.ini $PHP_INI_DIR/conf.d/default.ini

# Install dependencies
RUN set -xe \
    && apk add --no-cache icu-dev \
    && docker-php-ext-install pdo pdo_mysql intl pcntl \
    && docker-php-ext-enable opcache

CMD ["php-fpm"]

FROM composer:1.9 as composer

RUN rm -rf /var/www && mkdir /var/www
WORKDIR /var/www

COPY composer.* /var/www/

ARG APP_ENV=prod

RUN set -xe \
    && if [ "$APP_ENV" = "prod" ]; then export ARGS="--no-dev"; fi \
    && composer install --prefer-dist --no-scripts --no-progress --no-suggest --no-interaction $ARGS

COPY . /var/www

RUN composer dump-autoload --classmap-authoritative

FROM base AS app

ARG APP_ENV=prod
ARG APP_DEBUG=0

ENV APP_ENV $APP_ENV
ENV APP_DEBUG $APP_DEBUG

WORKDIR /var/www

COPY --from=composer /var/www/ /var/www/

# Memory limit increase is required by the dev image
RUN php -d memory_limit=256M bin/console cache:clear
RUN bin/console assets:install

FROM nginx:1.17-alpine AS web

COPY docker/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf

COPY --from=app /var/www/public /var/www/public
