FROM alpine:3.13

RUN NGINX_VERSION="1.19.10" \
    && PHP_VERSION="8.0.5" \
    && XDEBUG_VERSION="3.0.4" \
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
    git \
### Requirements for OpenSSL package
    perl \
    perl-text-template \
### Requirements for PHP package and PHP extensions
    libxml2-dev \
    postgresql-dev \
    ### with Curl
    && apk add --no-cache curl-dev \
    ### with GD
    && apk add --no-cache libwebp-dev libjpeg libpng-dev libxml2-dev libzip-dev imagemagick-dev \
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
    --enable-exif \
    --enable-fpm \
    --enable-sockets \
    --with-curl \
    --with-mysqli \
    --with-pdo-mysql \
    --with-pgsql \
    --with-pdo-pgsql \
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
### Install Imagick
    && cd /home/distr \
    && git clone https://github.com/Imagick/imagick \
    && cd imagick \
    && phpize && ./configure && make && make install \
### Install composer
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer \
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
