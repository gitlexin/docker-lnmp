FROM php:7.2.27-fpm-buster
MAINTAINER lexin "772821140@qq.com"

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#替换镜像源 有些包更换了镜像之后无法正常build，所以改为默认注释，嫌慢可以自己打开试试
#ENV SOURCELIST_FROM=qinghua
#COPY ./pkg/${SOURCELIST_FROM}_sourcelist /home/sources.list
#RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak && mv /home/sources.list /etc/apt/

# 更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        git \
        procps \
        vim \
        curl \
        wget \
        lsof \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
		libssl-dev \
        libxml2-dev \
        libgpgme11-dev \
        libmemcached-dev libmsgpack-dev libmsgpackc2 zlib1g-dev  \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) gd zip pdo_mysql opcache mysqli bcmath soap sockets \
        && rm -r /var/lib/apt/lists/* \
        &&  pecl install gnupg && echo "extension=gnupg.so" > /usr/local/etc/php/conf.d/gnupg.ini \
        &&  pecl install memcached && echo "extension=memcached.so" > /usr/local/etc/php/conf.d/memcached.ini \
        && echo 'yes' | pecl install memcache && echo "extension=memcache.so" > /usr/local/etc/php/conf.d/memcache.ini \
        &&  pecl install redis && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini

# 安装 Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

# 安装xdebug
#RUN pecl install xdebug && \
#    docker-php-ext-enable xdebug
#COPY ./pkg/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini


# 安装 Swoole
RUN cd /home \
    && git clone https://gitee.com/swoole/swoole.git \
    && cd swoole \
    && phpize \
    && ./configure --enable-openssl -with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini

# sdebug 和xdebug一样 是为了配合swoole使用的
RUN cd ~ && curl -fsSL 'https://github.com/mabu233/sdebug/archive/sdebug_2_6-beta.tar.gz' -o sdebug.tar.gz \
    && mkdir -p sdebug \
    && tar -xf sdebug.tar.gz -C sdebug --strip-components=1 \
    && rm sdebug.tar.gz \
    && ( \
        cd sdebug && phpize && ./configure --enable-xdebug --with-php-config=/usr/local/bin/php-config && make clean && make && make install \
    ) \
    && rm -r sdebug
COPY ./pkg/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# 写权限
WORKDIR /data
RUN usermod -u 1000 www-data

