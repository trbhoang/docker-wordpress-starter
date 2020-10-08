#
# Ref: https://hub.docker.com/r/easyengine/php7.4/dockerfile
#

FROM php:7.4-fpm

RUN set -ex && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
    imagemagick \
    libc-client-dev \
    libfreetype6-dev \
    libjpeg-dev \
    libjpeg62-turbo-dev \
    libkrb5-dev \
    libmagickwand-dev \
    libmcrypt-dev \
    libicu-dev \
    libgmp-dev \
    libxml2-dev \
    libpng-dev \
    libzip-dev \
    libssl-dev \
    zip

RUN pecl install imagick && \
    docker-php-ext-enable imagick &&\
    docker-php-ext-install exif && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-configure zip && \
    docker-php-ext-install gd && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install opcache && \
    docker-php-ext-install intl && \
    docker-php-ext-install zip && \
    docker-php-ext-install bcmath && \
    docker-php-ext-configure gmp && \
    docker-php-ext-install gmp

RUN apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false && \
    rm -rf /var/lib/apt/lists/*



# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.interned_strings_buffer=8'; \
    echo 'opcache.max_accelerated_files=4000'; \
    echo 'opcache.revalidate_freq=2'; \
    echo 'opcache.fast_shutdown=1'; \
    echo 'opcache.enable_cli=1'; \
    } > /usr/local/etc/php/conf.d/opcache-recommended.ini

# install Wordpress
ENV WORDPRESS_VERSION 5.5.1
ENV WORDPRESS_UPSTREAM_VERSION 5.5.1
ENV WORDPRESS_SHA1 d3316a4ffff2a12cf92fde8bfdd1ff8691e41931

# upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
RUN curl -o wordpress.tar.gz -SL https://wordpress.org/wordpress-${WORDPRESS_UPSTREAM_VERSION}.tar.gz \
    && echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c - \
    && tar -xzf wordpress.tar.gz -C /usr/src/ \
    && rm wordpress.tar.gz \
    && chown -R www-data:www-data /usr/src/wordpress

COPY custom.ini /usr/local/etc/php/conf.d/custom.ini
COPY docker-entrypoint.sh /entrypoint.sh

WORKDIR /var/www/html
ENTRYPOINT ["/entrypoint.sh"]
EXPOSE 9000
CMD ["php-fpm"]