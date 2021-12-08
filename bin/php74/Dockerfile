# Official Ubuntu 20.04 focal latest
FROM ubuntu:20.04

LABEL maintainer="Github PoulsQ"

# Env var for all the script should be here
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV TZ=Europe/Paris

# Only port 80 for a local engine
EXPOSE 80

# Working dir, cannot be changed
WORKDIR /app

# Update machine TZ
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update -qq && \
    apt-get install -qy \
    sudo \
    tzdata

# Setup Europe/Paris
RUN sudo dpkg-reconfigure --frontend noninteractive tzdata

# Install PHP + Apache
RUN apt-get update -qq && \
    apt-get install -qy \
    php7.4-cli php7.4 libpcre3-dev php7.4-dev php7.4-mysql php7.4-sqlite \
    php7.4-mysqli php7.4-curl php7.4-tokenizer php7.4-json  \
    php7.4-zip php7.4-intl php7.4-mbstring php7.4-gettext php7.4-calendar php7.4-exif  \
    apache2

# Install Freetype
#RUN apt-get -y update && \
#    apt-get --no-install-recommends install -y libfreetype6-dev \
#libjpeg62-turbo-dev \
#libpng-dev && \
#    rm -rf /var/lib/apt/lists/* && \
#    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg && \
#    docker-php-ext-install gd

# Install phalcon
RUN cd /usr/src
RUN pecl install phalcon-4.1.2
RUN pecl install apcu

# Install package git, unzip & zip are for composer
RUN apt-get update -qq && \
    apt-get install -qy \
    git \
    gnupg \
    unzip \
    libonig-dev \
    libxml2-dev \
    zip && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Create custom dirs
# Sample for custom dirs : RUN mkdir -p /home/myproject/public

# Create logs dirs / files
RUN mkdir /home/logs
RUN > /home/logs/php_error_log
RUN sudo chmod 755 /home/logs/php_error_log

# Copy .ini configs
COPY config/php74/php.ini /etc/php/7.4/cli/php.ini
COPY config/php74/php.ini /etc/php/7.4/apache2/php.ini
COPY config/php74/phalcon.ini /etc/php/7.4/mods-available/phalcon.ini
RUN ln -s /etc/php/7.4/mods-available/phalcon.ini /etc/php/7.4/cli/conf.d/50-phalcon.ini
RUN ln -s /etc/php/7.4/mods-available/phalcon.ini /etc/php/7.4/apache2/conf.d/50-phalcon.ini

# Installing Nano
RUN apt-get update -qq && \
    apt-get install -qy \
    nano

# Language packs for i18n mainly
RUN apt-get update -q && \
    apt-get install -qy language-pack-fr language-pack-en language-pack-de language-pack-es language-pack-it dos2unix

# Symfony tool
RUN curl -sS https://get.symfony.com/cli/installer | bash
RUN mv /root/.symfony/bin/symfony /usr/local/bin/symfony

# Install xdebug
RUN pecl install xdebug

# Install redis
RUN pecl install redis

# Install imagick
RUN apt-get update && \
    apt-get -y --no-install-recommends install --fix-missing libmagickwand-dev && \
    rm -rf /var/lib/apt/lists/* && \
    pecl install imagick

# Enable apache modules
RUN a2enmod rewrite headers

# Cleanup
RUN rm -rf /usr/src/*

CMD ["apachectl", "-D", "FOREGROUND"]
