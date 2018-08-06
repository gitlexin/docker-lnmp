# docker-lnmp

非常感谢 [jianyan74/lnmp-dockerfiles](https://github.com/jianyan74/lnmp-dockerfiles)项目， 本dockerfile是由jianyan74/lnmp-dockerfiles项目修改后得来。

在原项目基础上，增加了以下：
- 增加了环境变量，配置更轻松
- 增加了php5.6.32版本，实现多版本
- 修改了apt-get源，可使用阿里源或163源，编译更快速
- php5.6.32增加了memcache、opencc、opencc4php、xdebug扩展，php7.2如果需要的话，可以拷贝5.6.32dockerfile内的扩展编译内容到php7.2
- 预先下载好了编译包，节约时间


搭建lnmp环境

## 简介
用docker容器服务的方式搭建lnmp环境，易于维护、升级。使用前需了解Docker的基本概念，常用基本命令。
可以一条条命令执行docker命令来构建镜像，容器。这里推荐使用docker-compose来管理，执行项目，下面是使用流程。


相关软件版本：
- PHP 7.2
- PHP 5.6.32
- MySQL 5.7
- Nginx 1.12
- Redis 4.0
- Memcached 1.5 

用到的PHP扩展
- redis
- swoole latest
- memcached
- memcache
- opencc
- xdebug

#### 目录

目录 | 说明
---|---
--- app | 应用安装目录
--- data | mongo、mysql数据库文件存储
--- docs | 帮助文档
--- logs | nginx、mongo、mysql、php日志
--- sercices | 服务软件配置包
--- --- memcached | memcached配置及安装文件
--- --- mongo | memcached配置及安装文件
--- --- mysql | mysql配置及安装文件
--- --- nginx | nginx配置及安装文件
--- --- php56 | php5.6.32配置及安装文件
--- --- php72 | php7.2配置及安装文件
--- --- redis | redis配置及安装文件
--- --- docker-composer.yml | docker配置执行文件


## 使用
#### 1.安装Docker，Docker-compose  
- Docker，详见官方文档：https://docs.docker.com/engine/installation/linux/docker-ce/centos/
- docker-compose，文档：https://docs.docker.com/compose/install/
- 镜像加速，参考[docker使用国内镜像](https://github.com/yeasy/docker_practice/blob/master/install/mirror.md)
       不然下载镜像速度会卡的你怀疑人生
- 常见问题，必看。参考[这里](https://github.com/jianyan74/lnmp-dockerfiles/blob/master/docs/issue.md)
```
sudo pip install -U docker-compose
```

#### 2.下载docker-lnmp
直接clone：
```
git clone git@github.com:gitlexin/docker-lnmp.git
chmod -R 777 ./docker-lnmp/logs
cd docker-lnmp/services
```
拷贝.example.env为.env，根据自己的情况修改其中的配置

#### 3.docker-compose构建项目
进行docker-compose.yml所在文件夹：
执行命令：
```
docker-compose up
```  

如果没问题，下次启动时可以以守护模式启用，所有容器将后台运行：  
```
docker-compose up -d
``` 

使用 docker-compose 基本上就这么简单，Docker 就跑起来了，用 stop，start 关闭开启容器服务。  
更多的是在于编写 dockerfile 和 docker-compose.yml 文件。 

可以这样关闭容器并删除服务：
```
docker-compose down
```

#### 4. Demo站点搭建

进入app目录将你的项目文件拷贝到其中(项目文件会被映射到容器中的/data/www目录下)



域名解析

找到 `services/nginx/conf.d` 下的 example.conf 里修改配置
```
server_name example.com;
root /data/www/example;
access_log  /var/log/nginx/example.log;
```
注意重启一下nginx容器才能生效

## 我自己的常用命令
```
# 开启所有容器
docker-compose up -d
# 关闭所有容器
docker-compose down
# 重启某个容器
docker-compose restart nginx|php-fpm56等service名
# 进入容器
docker-compose exec -it 容器id /bin/bash
```

## 问题反馈

在使用中有任何问题，欢迎反馈给我

## 引用

[zPhal-dockerfiles](https://github.com/ZpGuo/zPhal-dockerfiles)

## 学习文档
[Docker 配置详解](https://www.jianshu.com/p/2217cfed29d7)

[Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

[Docker 微服务教程](http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html)

