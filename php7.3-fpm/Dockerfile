FROM php:7.3-fpm

# Source Thomas Glachant
# https://github.com/thomasglachant/docker/blob/master/php/7.1/fpm/Dockerfile
MAINTAINER Jessy Lenne <jessy.lenne@live.fr>

## Vars
ENV APCU_VERSION=5.1.8
ENV CSFIXER_VERSION=2.3.1

# Xdebug default config
ENV PHP_XDEBUG_ENABLED=0
ENV PHP_XDEBUG_HOST=0
ENV PHP_XDEBUG_IDEKEY=PHPSTORM
ENV PHP_XDEBUG_PORT=9000
ENV PHP_XDEBUG_MAX_NESTING_LEVEL=1024

# PHP IDE CONFIG (for xdebug : do not edit)
ENV PHP_IDE_CONFIG="serverName=docker"

# https://getcomposer.org/doc/03-cli.md#composer-allow-superuser
ENV COMPOSER_ALLOW_SUPERUSER 1

# Instal php requirements
RUN apt-get update && apt-get install -y --no-install-recommends \
        # Common
        git curl \
        libpcre3-dev \
        # GD requirements
        libjpeg62-turbo-dev libpng-dev libfreetype6-dev \
        # curl requirements
        libcurl3-dev \
        # intl requirements
        libicu-dev \
        # dom requirements
        libxml2-dev \
        # bz2 requirements
        libbz2-dev \
        # phar requirements
        libssl-dev \
        # tidy requirements
        libtidy-dev \
        # xsl requirements
        libxslt1-dev \
        # zip requirements
        libzip-dev \
        zlib1g-dev \
        # yaml requirements
        libyaml-dev
        
RUN docker-php-ext-configure \
        gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install -j$(nproc) \
        iconv gd curl pdo pdo_mysql fileinfo intl json mbstring \
        dom bz2 mysqli pcntl phar posix soap tidy tokenizer xsl bcmath \
        ctype exif sockets opcache simplexml xml

RUN docker-php-ext-install -j$(nproc) \
        zip

# Clean apt cache
RUN  rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove


# Install composer
COPY docker-install-composer /usr/local/bin/docker-install-composer
RUN chmod +x /usr/local/bin/docker-install-composer
RUN docker-install-composer \
    && mv composer.phar /usr/bin/composer

# Install php-cs-fixer
RUN curl -L http://cs.sensiolabs.org/download/php-cs-fixer-v2.phar -o php-cs-fixer \
    && chmod a+x php-cs-fixer \
    && mv php-cs-fixer /usr/local/bin/php-cs-fixer


# Set workdir as project root
WORKDIR /var/www/site

# Set user for permissions
RUN usermod -u 1000 www-data

# Add custom start script
COPY docker-app-start.sh /usr/local/bin/docker-app-start
RUN chmod +x /usr/local/bin/docker-app-start
CMD ["docker-app-start"]

# Add php custom config
COPY php.ini /usr/local/etc/php/php.ini

##<autogenerated>##
## FPM config ##
# Add custom fpm config
RUN rm -f /usr/local/etc/php-fpm.d/www.conf
COPY www.conf /usr/local/etc/php-fpm.d/www.conf