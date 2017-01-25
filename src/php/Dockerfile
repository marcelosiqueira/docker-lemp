FROM php:7-fpm
MAINTAINER Marcelo Siqueira <eu@marcelosiqueira.com.br>

# Install utilities
RUN apt-get update \
    && apt-get install -y \
        php-apc \
        imagemagick \
        graphicsmagick \
        zip \
        unzip \
        sudo \
        less \
        mysql-client \
        git \
        locales \
    && rm -rf /var/lib/apt/lists/*

# Set the locale
RUN echo "pt_BR.UTF-8 UTF-8" >> /etc/locale.gen \
   && locale-gen pt_BR.utf8 \
   && /usr/sbin/update-locale LANG=pt_BR.UTF-8

RUN echo "America/Sao_Paulo" > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata

# gd
RUN buildRequirements="libpng12-dev libjpeg-dev libfreetype6-dev" \
    && apt-get update && apt-get install -y ${buildRequirements} \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/lib \
    && docker-php-ext-install gd \
    && apt-get purge -y ${buildRequirements} \
    && rm -rf /var/lib/apt/lists/*

# pdo_mysql
RUN docker-php-ext-install pdo_mysql

# mysqli
RUN docker-php-ext-install mysqli

# mcrypt
RUN runtimeRequirements="re2c libmcrypt-dev" \
    && apt-get update && apt-get install -y ${runtimeRequirements} \
    && docker-php-ext-install mcrypt \
    && rm -rf /var/lib/apt/lists/*

# mbstring
RUN docker-php-ext-install mbstring

# intl
RUN buildRequirements="libicu-dev g++" \
    && apt-get update && apt-get install -y ${buildRequirements} \
    && docker-php-ext-install intl \
    && apt-get purge -y ${buildRequirements} \
    && runtimeRequirements="libicu52" \
    && apt-get install -y --auto-remove ${runtimeRequirements} \
    && rm -rf /var/lib/apt/lists/*

# imagick
RUN runtimeRequirements="libmagickwand-6.q16-dev --no-install-recommends" \
    && apt-get update && apt-get install -y ${runtimeRequirements} \
    && ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin/ \
    && pecl install imagick \
    && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini \
    && rm -rf /var/lib/apt/lists/*

# opcache
RUN docker-php-ext-install opcache

# zip
RUN docker-php-ext-install zip

# xml
RUN docker-php-ext-install xml

# yaml
RUN buildRequirements="libyaml-dev" \
    && apt-get update && apt-get install -y ${buildRequirements} \
    && pecl install yaml-2.0.0 \
    && echo "extension=yaml.so" > /usr/local/etc/php/conf.d/ext-yaml.ini \
    && apt-get purge -y ${buildRequirements} \
    && rm -rf /var/lib/apt/lists/*

# apcu + apcu-bc (For backwards compat)
RUN pecl install apcu \
    && echo "extension=apcu.so" > /usr/local/etc/php/conf.d/ext-apcu.ini  \
    && pecl install apcu_bc-beta \
    && echo "extension=apc.so" >> /usr/local/etc/php/conf.d/ext-apcu.ini

# composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer.phar
RUN mv /usr/local/bin/composer.phar /usr/local/bin/composer
RUN chmod +x /usr/local/bin/composer

RUN sed -i -e "s/;pm.max_children\s*=\s*yes/catch_workers_output = yes/g" /usr/local/etc/php-fpm.conf
RUN sed -i -e "s/pm.max_children = 5/pm.max_children = 3/g" /usr/local/etc/php-fpm.conf


# RUN mkdir -p /var/www/html

VOLUME ["/var/www/html"]

EXPOSE 9000

CMD ["php-fpm"]
