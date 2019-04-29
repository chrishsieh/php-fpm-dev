FROM php:fpm

ENV BUILD_DEPS="cmake libicu-dev libfreetype6-dev libjpeg62-turbo-dev libpng-dev libzip-dev libmagickwand-dev" \
    RUN_DEPS="gettext jq figlet zip git unzip locales locales-all wget curl \
            libfreetype6 libjpeg62-turbo libpng16-16 libzip4 ruby-full nodejs mysql-client libmagickwand-6.q16-3 \
            ca-certificates"

RUN set -x \
# ---- Installing Build dependencies ----
    && DEBIAN_FRONTEND=noninteractive apt-get update -qq \
    && DEBIAN_FRONTEND=noninteractive apt-get install -qq -y --no-install-recommends --no-install-suggests apt-utils ca-certificates \
    && DEBIAN_FRONTEND=noninteractive apt-get install -qq -y --no-install-recommends --no-install-suggests ${BUILD_DEPS} \
# ---- Installing PHP Extension: gd ----
    && /usr/local/bin/docker-php-ext-configure gd --with-gd --with-png-dir=/usr --with-zlib-dir=/usr --with-jpeg-dir=/usr --with-freetype-dir=/usr --enable-gd-jis-conv \
    && NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && docker-php-ext-install -j${NPROC} \
        gettext mysqli pdo_mysql exif gd zip \
    && (rm -rf /usr/local/lib/php/test/gd || true) \
    && (rm -rf /usr/local/lib/php/doc/gd || true) \
# ---- Installing PHP Extension: imagick ----
	&& pecl install imagick \
	&& docker-php-ext-enable imagick \
	&& (rm -rf /usr/local/lib/php/test/imagick || true) \
	&& (rm -rf /usr/local/lib/php/doc/imagick || true) \
# ---- Installing PHP Extension: xdebug ----
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && (rm -rf /usr/local/lib/php/test/xdebug || true) \
    && (rm -rf /usr/local/lib/php/doc/xdebug || true) \
# ---- Removing Build dependencies ----
    && DEBIAN_FRONTEND=noninteractive apt-get purge -qq -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false ${BUILD_DEPS} \
# ---- Installing Running dependencies ----
    && curl -sL https://deb.nodesource.com/setup_8.x | bash -\
    && DEBIAN_FRONTEND=noninteractive apt-get install -qq -y --no-install-recommends --no-install-suggests ${RUN_DEPS} \
    && DEBIAN_FRONTEND=noninteractive apt-get purge -qq -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false apt-utils \
    && rm -rf /var/lib/apt/lists/* \
    && update-ca-certificates

# ---- Installing Running env ----
RUN set -x \
    && npm i npm@latest -g \
    && npm install -g grunt-cli webpack-cli \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer self-update \
    && gem install sass -v 3.4.25 \
    && composer global require hirak/prestissimo

COPY ./presetup /usr/local/bin
COPY ./php.ini /usr/local/etc/php/
RUN chmod +x /usr/local/bin/presetup

# Set work directory to the web host path
WORKDIR /var/www

# Run the configsetup file on container start
ENTRYPOINT ["/usr/local/bin/presetup"]
CMD ["php-fpm"]
