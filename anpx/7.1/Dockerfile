FROM alpine:3.9

RUN NGINX_VERSION="1.16.0" \
    && PHP_VERSION="7.1.30" \
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
    && apk add --no-cache libwebp-dev libjpeg libpng-dev \
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
    && rm /usr/local/nginx/conf/*.default /usr/local/nginx/conf/nginx.conf \
    && ln -sf /dev/stdout /usr/local/nginx/logs/access.log \
    && ln -sf /dev/stderr /usr/local/nginx/logs/error.log \
### Install PHP
    && cd /home/distr \
    && curl -L -o php-$PHP_VERSION.tar.bz2 http://de2.php.net/get/php-$PHP_VERSION.tar.bz2/from/this/mirror \
    && tar xjf php-$PHP_VERSION.tar.bz2 \
    && cd php-$PHP_VERSION \
    && EXTENSION_DIR=/usr/php/modules ./configure \
    --prefix=/usr \
    --with-gd \
    --enable-mbstring \
    --with-curl \
    --with-mysqli \
    --with-pdo-mysql \
    --enable-fpm \
    --with-openssl \
    --with-zlib \
    --enable-zip \
    --with-config-file-path=/usr/etc \
    && make && make install \
    && mkdir -p /usr/php/modules \
### Install XDebug
    && pear config-set ext_dir /usr/php/modules \
    && pecl install xdebug \
### Install Composer
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
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
