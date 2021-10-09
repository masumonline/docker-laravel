FROM php:7.4.23-fpm-alpine3.13

ENV COMPOSER_ALLOW_SUPERUSER=1 \
    COMPOSER_DISABLE_XDEBUG_WARN=1 \
    PHPREDIS_VERSION=5.3.4
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php

RUN set -xe \
 && apk add --no-cache --virtual .build-deps \            
            tzdata \
	    $PHPIZE_DEPS \
 && apk add gnu-libiconv --update-cache \
 && cp /usr/share/zoneinfo/UTC /etc/localtime \
 && echo 'UTC+6' > /etc/localtime \
 && apk add --no-cache \
	openssl-dev \
        bash \
        freetype-dev \
        libpng-dev \
        libjpeg-turbo-dev \
        sqlite-dev \
        curl \
	curl-dev \
        libsodium-dev \
        icu-dev \
        libxml2-dev \
        recode-dev \
        libxslt-dev \
        git \
        postgresql-client \
        postgresql-dev \
        openssh-client \
                libzip-dev \
                libgcrypt-dev \
        oniguruma-dev \
     && apk --update --no-cache add grep \
  && mkdir -p /usr/src/php/ext/redis \
 && curl -L https://github.com/phpredis/phpredis/archive/$PHPREDIS_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
 && echo 'redis' >> /usr/src/php-available-exts \
    && docker-php-ext-configure gd \
      --with-freetype=/usr/include/ \
      --with-jpeg \
 && docker-php-ext-install \
    	gd \
        bcmath \
        opcache \
        iconv \
        mysqli \
        pdo \
        pdo_mysql \
        pdo_sqlite \
        pdo_pgsql \
        zip \
        xml \
        xsl \
        intl \
        json \
        mbstring \
        curl \
        simplexml \
        soap \
        bcmath \
        redis \
     && docker-php-ext-install sodium \
 && apk del .build-deps \
 && rm -rf /tmp/* /var/cache/apk/* 

    RUN echo "memory_limit = 128M" > /usr/local/etc/php/conf.d/custom.ini \
    && echo "max_execution_time = 60" >> /usr/local/etc/php/conf.d/custom.ini \
    && echo "error_reporting=E_ALL" >> /usr/local/etc/php/conf.d/custom.ini \
    && echo "upload_max_filesize=20M" >> /usr/local/etc/php/conf.d/custom.ini \
    && echo "post_max_size = 21M" >> /usr/local/etc/php/conf.d/custom.ini \
    && echo "log_errors=On" >> /usr/local/etc/php/conf.d/custom.ini \
    && echo "error_log = /proc/self/fd/2" >> /usr/local/etc/php/conf.d/custom.ini \
    && echo "expose_php = Off" >> /usr/local/etc/php/conf.d/custom.ini \
    && echo "pm.status_path = /status" >> /usr/local/etc/php-fpm.d/zz-docker.conf \
    && echo "ping.path = /ping" >> /usr/local/etc/php-fpm.d/zz-docker.conf \
    && echo "catch_workers_output = yes" >> /usr/local/etc/php-fpm.d/zz-docker.conf 

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
#RUN composer global require hirak/prestissimo --no-plugins --no-scripts

RUN addgroup -S sudo \
    && adduser -h /home/web -u 1000 -S -s /bin/bash -G sudo web \
    && echo "%sudo ALL=NOPASSWD:ALL" > /etc/sudoers