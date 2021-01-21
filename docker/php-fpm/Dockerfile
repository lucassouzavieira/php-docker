FROM php:7.4-fpm-alpine

COPY wait-for-it.sh /usr/bin/wait-for-it

RUN chmod +x /usr/bin/wait-for-it

RUN apk --update --no-cache add git

RUN docker-php-ext-install pdo_mysql

ENV XDEBUG_VERSION=2.9.8
ENV HOSTNAME=er22g

RUN apk --no-cache add --virtual .build-deps \
        g++ \
        autoconf \
        make && \
    pecl install xdebug-${XDEBUG_VERSION} && \
    docker-php-ext-enable xdebug && \
    apk del .build-deps && \
    rm -r /tmp/pear/* && \
    echo -e "zend_extension=xdebug.so\n\
        xdebug.remote_enable=1\n\
        xdebug.remote_autostart=1\n\
        xdebug.remote_port=9000\n\
        xdebug.remote_log=/var/www/html/xdebug.log\n\
        xdebug.remote_host=${HOSTNAME}" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

COPY --from=composer /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/

CMD composer install ; wait-for-it database:3306 -- bin/console doctrine:migrations:migrate ;  php-fpm 

EXPOSE 9000

