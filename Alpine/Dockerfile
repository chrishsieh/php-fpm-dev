FROM php:fpm-alpine

RUN apk add --update \
        icu icu-dev \
        gettext gettext-dev \
        jq figlet ncurses \
        zip libzip libzip-dev \
        freetype freetype-dev \
        libjpeg-turbo libjpeg-turbo-dev \
        libpng libpng-dev \
        libmcrypt libmcrypt-dev \
        gcc make libc-dev autoconf \
        git ruby-full npm composer python g++ && \
    docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-configure zip \
        --with-libzip && \
    NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) && \
    docker-php-ext-install -j${NPROC} \
        intl gettext \
        mysqli pdo_mysql \
        exif \
        gd \
        zip && \
    pecl channel-update pecl.php.net && pecl install mcrypt-1.0.1 && \
        docker-php-ext-enable mcrypt &&\
    pecl install xdebug && docker-php-ext-enable xdebug && \
    apk del \
        icu-dev gettext-dev \
        freetype-dev libjpeg-turbo-dev libpng-dev \
        libzip-dev \
        libmcrypt-dev \
        autoconf && \
    npm i npm@latest -g && \
    composer self-update && \
    gem install sass -v 3.4.25

COPY ./presetup /usr/local/bin
COPY ./php.ini /usr/local/etc/php/
RUN chmod +x /usr/local/bin/presetup

# Set work directory to the web host path
WORKDIR /var/www

# Run the configsetup file on container start
ENTRYPOINT ["/usr/local/bin/presetup"]
CMD ["php-fpm"]
