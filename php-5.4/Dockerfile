# Based on ubuntu 14.04
FROM ubuntu:15.10


MAINTAINER Frank Lemanschik <frank@dspeed.eu>
ENV PHP_VERSION 5.4.45
ENV PHP_HASH 185f67f1ca652b18bc0cca9d7edbde7c
ENV XCACHE_VERSION 3.2.0
ENV XCACHE_HASH 3655bd20483dc23d24f87f9dc924a62e



# Update everything & install prequesites
RUN apt-get update \
    && apt-get -y install build-essential wget bzip2 \
		libreadline6-dev librecode-dev libsqlite3-dev libssl-dev libxml2-dev \
        libpng-dev libmcrypt-dev libmcrypt4 libmhash-dev libmysqlclient-dev libpspell-dev \
        libjpeg-dev zlib1g-dev libfreetype6-dev libfontconfig1-dev libgmp-dev \
        libgpg-error-dev uuid-dev libsnmp-dev libc-client2007e-dev \
        libaspell-dev libbz2-dev libc-client2007e-dev flex bison libsqlite3-dev libpq-dev \
        libcurl4-openssl-dev libmagickwand-dev libgd2-xpm-dev autoconf imagemagick \
        graphicsmagick libdb-dev libtidy-dev libxslt-dev libgeoip-dev libicu-dev spawn-fcgi re2c

# Ubuntu Fixes to compile
## Copy libc-client to x86_64-dir
RUN cp /usr/lib/libc-client.so /usr/lib/x86_64-linux-gnu/
RUN cp /usr/lib/libc-client.a /usr/lib/x86_64-linux-gnu/
## Symlink gmp header
RUN ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h 


# Download PHP

RUN mkdir -p /tmp/install/ && \
    cd /tmp/install && \
    wget http://de2.php.net/distributions/php-${PHP_VERSION}.tar.bz2 && \
    echo "${PHP_HASH}  php-${PHP_VERSION}.tar.bz2" > md5sums && \
    md5sum -c md5sums && \
    tar xfj php-${PHP_VERSION}.tar.bz2 && \
    cd php-${PHP_VERSION} && \
    ./configure \
        --disable-cgi \
        --disable-debug \
        --disable-rpath \
        --enable-fpm \
        --enable-inline-optimization \
        --enable-mysqlnd \
        --enable-sockets \
        --enable-sysvsem  \
        --enable-sysvshm \
        --enable-pcntl \
        --enable-mbregex \
        --enable-zip \
        --enable-gd-native-ttf \
        --enable-bcmath \
        --enable-ctype \
        --enable-exif \
        --enable-ftp \
        --enable-mbstring \
        --enable-intl \
        --enable-soap \
        --with-fpm-user=www-data \
        --with-fpm-group=www-data \
        --with-config-file-path=/etc/php5 \
        --with-config-file-scan-dir=/etc/php5/conf.d \
        --with-libdir=lib/x86_64-linux-gnu \
        --with-curl \
	--with-openssl \
	--with-readline \
	--with-recode \
        --with-bz2 \
        --with-zlib \
        --with-pcre-regex \
        --with-mysqli  \
        --with-mysql \
        --with-gd \
        --with-mcrypt \
        --with-mhash  \
        --with-snmp=shared \
        --with-freetype-dir=shared,/usr  \
        --with-jpeg-dir=/usr  \
        --with-db4 \
        --with-iconv  \
        --with-gettext \
        --with-pgsql \
        --with-pdo-mysql  \
        --with-tidy \
        --with-xsl \
        --with-gmp \
        --with-pspell \
    && make -j 4 \
    && make install \
    && rm -rf /tmp/install

RUN mkdir -p /etc/php5/conf.d/

# Set location and timestamp
RUN echo "date.timezone=Europe/Berlin\ndate.default_latitude=49.08\ndate.default_longitude=11.22" > /etc/php5/conf.d/10_timezone.ini

# Build module xhprof-0.9.4
RUN mkdir -p /tmp/install/ && \
    cd /tmp/install && \
    wget http://pecl.php.net/get/xhprof-0.9.4.tgz && \
    echo "ab4062a7337e3bdaa2fd7065a7942b8d  xhprof-0.9.4.tgz" > md5sums && \
    md5sum -c md5sums && \
    tar xfz xhprof-0.9.4.tgz && \
    cd xhprof-0.9.4/extension && \
    phpize && \
    ./configure && \
    make && \
    make install && \
    rm -rf /tmp/install

# Build module xcache
RUN mkdir -p /tmp/install/ && \
    cd /tmp/install && \
    wget http://xcache.lighttpd.net/pub/Releases/${XCACHE_VERSION}/xcache-${XCACHE_VERSION}.tar.bz2 && \
    echo "${XCACHE_HASH}  xcache-${XCACHE_VERSION}.tar.bz2" > md5sums && \
    md5sum -c md5sums && \
    tar xfj xcache-${XCACHE_VERSION}.tar.bz2 && \
    cd xcache-${XCACHE_VERSION} && \
    phpize && \
    ./configure && \
    make && \
    make install && \
    rm -rf /tmp/install

RUN pecl install channel://pecl.php.net/pecl_http-2.0.7
RUN echo "extension=propro.so\nextension=raphf.so\nextension=http.so" > /etc/php5/conf.d/30_http.ini

RUN pecl install channel://pecl.php.net/xdebug-2.2.5



RUN pecl install channel://pecl.php.net/apc-3.1.13
RUN echo "extension=apc.so\napc.enabled=1" > /etc/php5/conf.d/30_apc.ini

RUN pecl install channel://pecl.php.net/mailparse-2.1.6
RUN echo "extension=mailparse.so" > /etc/php5/conf.d/30_mailparse.ini

RUN pecl install channel://pecl.php.net/geoip-1.0.8
RUN echo "extension=geoip.so" > /etc/php5/conf.d/30_geoip.ini

RUN pecl install channel://pecl.php.net/memcache-3.0.8
RUN echo "extension=memcache.so" > /etc/php5/conf.d/30_memcache.ini

RUN pecl install channel://pecl.php.net/imagick-3.3.0
RUN echo "extension=imagick.so" > /etc/php5/conf.d/30_imagick.ini

# Install ioncube
RUN mkdir -p /tmp/install/ && \
    cd /tmp/install && \
    wget http://downloads3.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.bz2 && \
    tar xfj ioncube_loaders_lin_x86-64.tar.bz2 && \
    mv ioncube/ioncube_loader_lin_5.4.so $(php-config --extension-dir)/ioncube_loader.so && \
    rm -rf /tmp/install

# Enable ioncube
RUN echo "zend_extension=$(php-config --extension-dir)/ioncube_loader.so" > /etc/php5/conf.d/20_ioncube_loder.ini

# Test if there is stderr output
RUN bash -c "[[ $(php -i 2>&1 > /dev/null | wc -l) -ne 0 ]] && php -i > /dev/null && exit 1; exit 0"

# Install sendmail replacement / set ip address of real mailserver to 172.17.42.1
RUN mkdir -p /tmp/install/ && \
    cd /tmp/install && \
    wget https://github.com/simonswine/mini_sendmail/archive/1.3.8-1.tar.gz  && \
    tar xvfz 1.3.8-1.tar.gz && \
    cd mini_sendmail*/ && \
    make SMTP_HOST=172.17.42.1 && \
    cp -v mini_sendmail /usr/sbin/sendmail && \
    rm -rf /tmp/install
CMD []
