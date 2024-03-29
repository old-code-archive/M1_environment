FROM php:7.1-fpm

RUN apt-get update && apt-get install -y \
  cron \
  git \
  gzip \
  libfreetype6-dev \
  libicu-dev \
  libjpeg62-turbo-dev \
  libmcrypt-dev \
  libpng-dev \
  libxslt1-dev \
  lsof \
  mysql-client \
  vim \
  zip

RUN docker-php-ext-configure \
  gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install \
  bcmath \
  gd \
  intl \
  mbstring \
  mcrypt \
  opcache \
  pdo_mysql \
  soap \
  xsl \
  zip

RUN pecl channel-update pecl.php.net \
  && pecl install xdebug \
  && docker-php-ext-enable xdebug \
  && sed -i -e 's/^zend_extension/\;zend_extension/g' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

RUN curl -sS https://getcomposer.org/installer | \
  php -- --install-dir=/usr/local/bin --filename=composer
RUN curl -SL https://raw.githubusercontent.com/colinmollenhour/modman/master/modman -o modman \
    && chmod +x ./modman \
    && mv ./modman /usr/local/bin/
# create user
RUN groupadd -g 1000 app \
 && useradd -g 1000 -u 1000 -d /var/www -s /bin/bash  -p $(openssl passwd -1 app) app
RUN apt-get install -y sudo \
    && apt-get install -y nano
RUN usermod -aG sudo app

################# install imagick extension (to correctly show images) #################
RUN apt-get install -y libmagickwand-dev imagemagick && \
    echo -e "\n" | pecl install imagick
########################################################################################

RUN apt-get install -y gnupg \
  && curl -sL https://deb.nodesource.com/setup_8.x | bash - \
  && apt-get install -y nodejs \
  && mkdir /var/www/.config /var/www/.npm \
  && chown app:app /var/www/.config /var/www/.npm \
  && ln -s /var/www/html/node_modules/grunt/bin/grunt /usr/bin/grunt

RUN printf '* *\t* * *\tapp\t%s/usr/local/bin/php /var/www/html/cron.php\n#\n' >> /etc/crontab

COPY conf/php.ini /usr/local/etc/php/
COPY conf/zz-php-fpm.conf /usr/local/etc/php-fpm.d/
COPY bin/cronstart.sh /usr/local/bin/

USER root:root
VOLUME /var/www

WORKDIR /var/www/html

EXPOSE 9000