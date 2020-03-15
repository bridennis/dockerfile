FROM alpine:3.9.5

RUN NGINX_VERSION="1.16.1" \
    && PHP_VERSION="7.4.3" \
    && XDEBUG_VERSION="2.9.3" \
    && apk add --update --no-cache \
    curl \
    make \
    cmake \
    autoconf \
    libc-dev \
    gcc \
    linux-headers \
    pcre-dev \
    zlib-dev \
### Requirements for OpenSSL package
    perl \
    perl-text-template \
### Requirements for PHP package and PHP extensions
    libxml2-dev \
    ### with Curl
    && apk add --no-cache curl-dev \
    ### with GD
    && apk add --no-cache libwebp-dev libjpeg libpng-dev libxml2-dev libzip-dev \
### Requirements for Nginx
    && apk add --no-cache sqlite-dev oniguruma-dev \
    ###
    && mkdir /home/distr \
### Set timezone Europe/Moscow
    && apk add --no-cache -U tzdata \
    && cp /usr/share/zoneinfo/Europe/Moscow /etc/localtime \
### Install Nginx
    && cd /home/distr \
    && curl -LO https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz \
    && tar xzf nginx-$NGINX_VERSION.tar.gz \
    && cd nginx-$NGINX_VERSION \
    && ./configure --user=nobody --with-http_ssl_module --with-http_v2_module \
    && make && make install \
    && rm -Rf /usr/local/nginx/html /usr/local/nginx/logs \
    && rm /usr/local/nginx/conf/*.default /usr/local/nginx/conf/nginx.conf \
### Install PHP
    && cd /home/distr \
    && curl -L -o php-$PHP_VERSION.tar.bz2 http://de2.php.net/get/php-$PHP_VERSION.tar.bz2/from/this/mirror \
    && tar xjf php-$PHP_VERSION.tar.bz2 \
    && cd php-$PHP_VERSION \
    && EXTENSION_DIR=/usr/php/modules ./configure \
    --prefix=/usr \
    --enable-gd \
    --enable-mbstring \
    --with-curl \
    --with-mysqli \
    --with-pdo-mysql \
    --enable-fpm \
    --with-openssl \
    --with-zlib \
    --with-zip \
    --with-config-file-path=/usr/etc \
    && make && make install \
    && mkdir -p /usr/php/modules \
### Install XDebug
    && cd /home/distr \
    && wget https://xdebug.org/files/xdebug-$XDEBUG_VERSION.tgz \
    && tar xzf xdebug-$XDEBUG_VERSION.tgz \
    && cd xdebug-$XDEBUG_VERSION \
    && phpize \
    && ./configure --enable-xdebug \
    && make && make install \
### Clean up
    && apk del \
    perl-text-template \
    perl \
    gcc \
    linux-headers \
    make \
    cmake \
    autoconf \
    tzdata \
    && rm -Rf /home/distr \
    && rm -Rf /usr/etc/* \
    && mkdir -p /var/www/html \
### Start script
    && echo "#!/bin/sh" > /start.sh \
### Run PHP & Nginx
    && echo "/usr/sbin/php-fpm" >> /start.sh \
    && echo "/usr/local/nginx/sbin/nginx -c /usr/etc/nginx.conf" >> /start.sh \
    && chmod 770 /start.sh

WORKDIR /var/www/html

EXPOSE 80

CMD ["/start.sh"]
