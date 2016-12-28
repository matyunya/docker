FROM php:5.6-fpm

MAINTAINER Maxim Matyunin <matyunya@matyunya.com>

ENV DEBIAN_FRONTEND noninteractive

# Install php extensions
RUN buildDeps=" \
        libicu-dev \
        libmcrypt-dev \
        libpng12-dev \
        libpq-dev \
        libpspell-dev \
        libsasl2-dev \
        libsnmp-dev \
        libssl-dev \
        libxslt1-dev \
        zlib1g-dev \
        git \
        zip \
        php5-pgsql \
    " \
    && phpModules=" \
        curl gearman json memcache mysqli mysqlnd pdo pdo_pgsql readline xsl gd imagick mcrypt mongo mysql opcache pdo_mysql pgsql timezonedb \
    " \
    && echo "deb http://httpredir.debian.org/debian jessie contrib non-free" > /etc/apt/sources.list.d/additional.list \
    && apt-get update && apt-get install --yes --force-yes libjpeg62-turbo libmcrypt4 libmemcachedutil2 libpng12-0 libpq5  --no-install-recommends \
    && apt-get install --yes --force-yes $buildDeps --no-install-recommends \
    && docker-php-source extract \
    && cd /usr/src/php/ext/ \
    && ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-xpm-dir=/usr/include/ \
    && docker-php-ext-configure imap --with-imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-configure ldap --with-ldap-sasl \
    && docker-php-ext-install $phpModules \
    && printf "\n" | pecl install memcache \
    && printf "\n" | pecl install memcached \
    && for ext in $phpModules; do \
           rm -f /usr/local/etc/php/conf.d/docker-php-ext-$ext.ini; \
       done \
    && docker-php-source delete \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $buildDeps

COPY php.ini /usr/local/etc/php/

# Install additional packages
RUN apt-get update && apt-get install -y git msmtp-mta openssh-client --no-install-recommends && rm -r /var/lib/apt/lists/*

# Install composer and put binary into $PATH
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/ \
    && ln -s /usr/local/bin/composer.phar /usr/local/bin/composer

# Install phpunit and put binary into $PATH
RUN curl -sSLo phpunit.phar https://phar.phpunit.de/phpunit.phar \
    && chmod 755 phpunit.phar \
    && mv phpunit.phar /usr/local/bin/ \
    && ln -s /usr/local/bin/phpunit.phar /usr/local/bin/phpunit

# Install Node.js
#RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - \
# && apt-get install -y nodejs build-essential

COPY msmtprc /etc/
