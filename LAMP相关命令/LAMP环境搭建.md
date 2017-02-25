# Apache 

## 安装

    apt-get install apache2

## 测试安装是否成功

    apache -v

查看是否安装php支持模块

    cd /etc/apache2/mods-enabled
    ll

查看里面是不是有一个

    php7.0.load

的目录，如果没有，那么就要额外安装这个软件：

    apt-get install libapache2-mod-php7.0

使apache2开启对php7.0的支持。

# PHP7.0 安装

## 安装

    apt-get install php7.0
    /*下面安装php7.0对mysql的支持*/
    apt-get install php7.0-mysql

## 测试安装是否成功

    php7.0 -v 

# mysql 安装

## 安装

    apt-get install mysql-server

## 测试安装是否成功

安装成功会让你设置mysql的root用户的密码

# 查看所有 apt-get 可安装软件包

    apt-cache search all | grep 软件包名






