FROM php:7.4.11-fpm-alpine
COPY php.ini /usr/local/etc/php/conf.d/php.ini
# install composer etc.
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && \
    apk update && \
    apk add --no-cache -U autoconf make gcc musl-dev g++ zlib-dev libzip libzip-dev && \
    apk add --no-cache -U freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev nginx && \

    wget https://mirrors.aliyun.com/composer/composer.phar -O /usr/local/bin/composer && \
    chmod +x /usr/local/bin/composer && \
    composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ && \
    # php extensions gd below php74
    # docker-php-ext-configure gd \
    # --with-gd \
    # --with-freetype-dir=/usr/include/ \
    # --with-png-dir=/usr/include/ \
    # --with-jpeg-dir=/usr/include/ && \
    # php extensions gd with php74
    docker-php-ext-configure gd \
        --with-freetype \
        --with-jpeg && \
    NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) && \
    docker-php-ext-install -j${NPROC} gd && \
    # apk -v --pure del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev && \
    # php extensions for others
    docker-php-ext-install pdo pdo_mysql ctype iconv opcache calendar exif bcmath mysqli zip && \
    pecl install redis-5.3.5 && docker-php-ext-enable redis pdo_mysql && \
    apk -v --purge del --no-cache py-pip autoconf make gcc musl-dev g++ zlib-dev libzip-dev && \
    rm -rf /var/cache/apk/* && rm -rf /tmp/* && \
    # run nginx
    mkdir /run/nginx && \
    sed -i "s/www-data/nginx/g" /usr/local/etc/php-fpm.d/www.conf
# php extension config
COPY opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# init nginx config
COPY nginx.conf /etc/nginx/nginx.conf
COPY default.conf /etc/nginx/conf.d/default.conf
COPY www.conf /usr/local/etc/php-fpm.d/www.conf

# init entrypoint etc.
COPY entrypoint.sh /usr/bin/entrypoint.sh
COPY init.sh /usr/bin/init.sh
RUN chmod +x /usr/bin/entrypoint.sh
RUN chmod +x /usr/bin/init.sh

WORKDIR /var/www/html
ENTRYPOINT /usr/bin/entrypoint.sh