FROM php:fpm-alpine

# Make info file about this build
RUN set -x \
    && apk update \
    && apk add --update $PHPIZE_DEPS \
        icu icu-dev \
        gettext gettext-dev \
        jq figlet ncurses \
        zip libzip libzip-dev \
        freetype freetype-dev \
        libjpeg-turbo libjpeg-turbo-dev \
        libpng libpng-dev \
        git ruby-full npm composer python g++ mysql-client wget \
        libmemcached libmemcached-dev \
    && docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-configure zip \
        --with-libzip \
    && NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && docker-php-ext-install -j${NPROC} \
        intl gettext \
        mysqli pdo_mysql \
        exif \
        gd \
        zip \
    && pecl channel-update pecl.php.net \
    && pecl install xdebug-2.7.0RC2 \
    && docker-php-ext-enable xdebug \
    && (rm -rf /usr/local/lib/php/test/xdebug || true) \
    && (rm -rf /usr/local/lib/php/doc/xdebug || true) \
    && pecl install memcached \
    && docker-php-ext-enable memcached \
    && (rm -rf /usr/local/lib/php/test/memcached || true) \
    && (rm -rf /usr/local/lib/php/doc/memcached || true) \
    && apk del \
        icu-dev gettext-dev \
        freetype-dev libjpeg-turbo-dev libpng-dev \
        libzip-dev \
        autoconf \
        libmemcached-dev \
    && npm i npm@latest -g \
    && composer self-update \
    && composer global require hirak/prestissimo \
    && gem install sass -v 3.4.25

COPY ./presetup /usr/local/bin
COPY ./php.ini /usr/local/etc/php/
RUN chmod +x /usr/local/bin/presetup

# Set work directory to the web host path
WORKDIR /var/www

# Run the configsetup file on container start
ENTRYPOINT ["/usr/local/bin/presetup"]
CMD ["php-fpm"]
