#FROM php:5.4-apache
FROM drupaldocker/php:7.1-apache


# Set the locale
RUN apt-get clean && apt-get -y update && apt-get install -y locales && locale-gen en_US.UTF-8

# Set environment variables
ENV COMPOSER_ALLOW_SUPERUSER=1

# Install Ubuntu packages
RUN apt-get install -y \
        build-essential \
        curl \
        git \
        libcurl4-openssl-dev \
        libicu-dev \
        libmcrypt-dev \
        mysql-client \
        libpng-dev \
        mysql-client \
        ruby-full \
        software-properties-common \
        vim \
        net-tools \
        iputils-ping \
        ssh \
        zip

# Install PHP
RUN  docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-install \
      intl \
      mbstring \
      mcrypt \
      mysqli \
      pcntl \
      pdo_mysql \
      pdo_pgsql \
      pgsql \
      zip \
      opcache \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer

# Install Dev dependecies
RUN curl -fsSL 'https://xdebug.org/files/xdebug-2.5.5.tgz' -o xdebug.tar.gz \
        && mkdir -p xdebug \
        && tar -xf xdebug.tar.gz -C xdebug --strip-components=1 \
        && rm xdebug.tar.gz \
        && ( \
        cd xdebug \
        && phpize \
        && ./configure --enable-xdebug \
        && make -j$(nproc) \
        && make install \
        ) \
        && rm -r xdebug \
        && docker-php-ext-enable xdebug

COPY ./xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
COPY ./drupal-opcache.ini /usr/local/etc/php/conf.d/drupal-opcache.ini
COPY ./.bashrc /root/.bashrc

RUN su -c "gem install sass"

# Apache configuration
RUN a2enmod rewrite
RUN service apache2 restart
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Change uid and gid of apache to docker user uid/gid
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data

# Install package dependencies
WORKDIR /var/www/html
#COPY composer.json ./www
#RUN composer install && npm install --quiet