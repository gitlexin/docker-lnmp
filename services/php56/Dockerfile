FROM php:5.6.32-fpm
MAINTAINER lexin "772821140@qq.com"

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#替换镜像源
ENV SOURCELIST_FROM=aliyun
COPY ./pkg/${SOURCELIST_FROM}_sourcelist /home/sources.list
RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak && mv /home/sources.list /etc/apt/

# 更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        git \
        vim \
        curl \
        wget \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
		libssl-dev \
        libmcrypt-dev \
        libxml2-dev \
        libgpgme11-dev \
        doxygen \
        cmake \
        python \
        libmemcached-dev \
        libevent-dev \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) gd zip pdo_mysql opcache mysqli mysql sockets mcrypt bcmath mbstring opcache soap \
	    && pecl channel-update pecl.php.net \
        && pecl install gnupg && echo "extension=gnupg.so" > /usr/local/etc/php/conf.d/gnupg.ini \
        && pecl install redis-4.3.0 && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini \
        && pecl install memcache-3.0.8 && echo "extension=memcache.so" > /usr/local/etc/php/conf.d/memcache.ini \
        && pecl install memcached-2.2.0 && echo "extension=memcached.so" > /usr/local/etc/php/conf.d/memcached.ini \
        && pecl install xdebug-2.3.3 && docker-php-ext-enable xdebug \
        && rm -r /var/lib/apt/lists/*

COPY ./pkg/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# 安装opencc
COPY ./pkg/opencc.tar /home/opencc.tar
RUN tar zxvf /home/opencc.tar \
        && cd OpenCC \
        && make && make install

# 安装opencc4php扩展
COPY ./pkg/opencc4php.tar /home/opencc4php.tar
RUN tar zxvf /home/opencc4php.tar \
        && cd opencc4php \
        && phpize \
        && ./configure \
        && make && make install \
        && echo "extension=opencc.so" > /usr/local/etc/php/conf.d/opencc.ini

# 安装 Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

# 删除扩展包
RUN rm -f /home/opencc4php
RUN rm -f /home/OpenCC

# 写权限
WORKDIR /data
RUN usermod -u 1000 www-data
