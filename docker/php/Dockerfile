FROM php:7.3-fpm-alpine

ARG ENVIRONMENT

# Initial setup
RUN set -ex; \
    # 1) Install initial modules
    apk update; \
    apk add --no-cache \
        bash \
        sed \
        ghostscript; \
    # 2) Install php extension
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        freetype-dev \
        imagemagick-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        libzip-dev; \
    docker-php-ext-configure gd --with-freetype-dir=/usr --with-jpeg-dir=/usr --with-png-dir=/usr; \
    docker-php-ext-install -j "$(nproc)" \
        bcmath \
        exif \
        gd \
        mysqli \
        opcache \
        zip; \
    pecl install imagick-3.4.4; \
    docker-php-ext-enable imagick; \
    # if develop-environment, install xdebug.
    if [ "${ENVIRONMENT}" = "develop" ]; then \
        echo "pecl install xdebug"; \
        echo "docker-php-ext-enable xdebug"; \
    fi; \
    runDeps="$( \
        scanelf --needed --nobanner --format '%n#p' --recursive /usr/local/lib/php/extensions \
        | tr ',' '\n' \
        | sort -u \
        | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
        )"; \
    apk add --virtual .wordpress-phpexts-rundeps $runDeps; \
    apk del .build-deps;