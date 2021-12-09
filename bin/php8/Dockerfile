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

# Find php8.0 repo
RUN apt update -qq && \
    apt install lsb-release ca-certificates apt-transport-https software-properties-common -qy && \
    add-apt-repository ppa:ondrej/php

# Install PHP + Apache
RUN apt-get update -qq && \
    apt-get install -qy \
    php8.0-cli php8.0 libpcre3-dev php8.0-dev php8.0-mysql php8.0-sqlite \
    php8.0-mysqli php8.0-curl php8.0-tokenizer \
    php8.0-zip php8.0-intl php8.0-mbstring php8.0-gettext php8.0-calendar php8.0-exif  \
    apache2

# Install Freetype
#RUN apt-get -y update && \
#    apt-get --no-install-recommends install -y libfreetype6-dev \
#libjpeg62-turbo-dev \
#libpng-dev && \
#    rm -rf /var/lib/apt/lists/* && \
#    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg && \
#    docker-php-ext-install gd


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


# Create logs dirs / files
RUN mkdir /home/logs
RUN > /home/logs/php_error_log
RUN sudo chmod 755 /home/logs/php_error_log

# Copy .ini configs
COPY config/php8/php.ini /etc/php/8.0/cli/php.ini
COPY config/php8/php.ini /etc/php/8.0/apache2/php.ini

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

# Create custom dirs
#RUN mkdir -p /home/myproject/public

# Cleanup
RUN rm -rf /usr/src/*

CMD ["apachectl", "-D", "FOREGROUND"]
