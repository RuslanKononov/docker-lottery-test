FROM php:7.3.4-fpm
ARG TIMEZONE

RUN apt-get update && \
    apt-get install -y nano \
                        sudo

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
  && printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --version=1.8.0 --install-dir=/usr/local/bin --filename=composer

RUN mkdir /var/www/.composer && mkdir /var/www/.composer/cache && chmod 777 /var/www/.composer/cache

RUN  apt-get update && \
    apt-get install -y libpq-dev \
  libxml2-dev \
  libzip-dev \
  libpng-dev \
  zip \
  && docker-php-ext-configure zip --with-libzip \
  && docker-php-ext-install \
    zip \
    pdo_pgsql \
    soap \
    opcache \
    pcntl

RUN apt-get update && apt-get install -y autoconf g++ make git \
  && docker-php-source extract \
  && pecl install \
    apcu \
  && docker-php-ext-enable \
    apcu \
  && docker-php-source delete \
  && mkdir /src && cd /src && git clone https://github.com/xdebug/xdebug.git && cd xdebug && sh ./rebuild.sh && cd / && rm -fr /src \
  && rm -rf /tmp/* 

COPY conf.d/* /usr/local/etc/php/conf.d/

RUN echo "www-data:www-data" | chpasswd && adduser www-data sudo

RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER www-data

#RUN echo 'alias sf="php bin/console"' >> ~/.bashrc

WORKDIR /var/www/symfony