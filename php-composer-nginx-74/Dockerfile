FROM alpine:3.13

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.tencent.com/g' /etc/apk/repositories \
    && apk add --update --no-cache \
    php7 \
    php7-fileinfo \
    php7-curl \
    php7-phar \
    php7-fpm \
    php7-dom \
    php7-xmlwriter \
    php7-xml \
    php7-redis \
    php7-json \
    php7-ctype \
	php7-exif \
    php7-session \
    php7-pdo_mysql \
    php7-tokenizer \
    nginx \
    wget \
    && rm -f /var/cache/apk/* \
    && mkdir -p /run/nginx \
    && mv /usr/sbin/php-fpm7 /usr/sbin/php-fpm

# install composer
RUN wget https://mirrors.aliyun.com/composer/composer.phar -O /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

# init nginx php config
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY fpm.conf /etc/php7/php-fpm.d/www.conf
COPY php.ini /etc/php7/php.ini
COPY opcache.ini /etc/php7/conf.d/opcache.ini

# set work directory
WORKDIR /var/www/html