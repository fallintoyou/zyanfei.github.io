---
title: centos7.3搭建lnmp环境
date: 2018-09-19 14:12:09
tags:
categories: Linux
---
安装Mysql
---------

 - 1.1.MYSQL安装

下载mysql的repo源
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
安装mysql-community-release-el7-5.noarch.rpm包
rpm -ivh mysql-community-release-el7-5.noarch.rpm
安装MYSQL
sudo yum install -y  mysql-server
更改MYSQL用户权限：
sudo chown -R root:root /var/lib/mysql
重启服务：
systemctl restart mysql.service
登录，并修改密码：
mysql -u root
mysql > use mysql;
mysql > update user set password=password('123456') where user='root';
mysql > flush privileges;
mysql > exit;

安装Nginx
---------

 - 2.1nginx安装

下载对应当前系统版本的nginx包
wget http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
建立nginx的yum仓库（默认yum是没有nginx的）
rpm -ivh nginx-release-centos-7-0.el7.ngx.noarch.rpm
下载并安装nginx
yum install -y nginx
nginx启动
systemctl start nginx.service

安装php
-------

rpm 安装 Php7 相应的 yum源
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
安装php7.0
yum install -y php70w
安装php扩展
yum install -y  php70w-mysql.x86_64   php70w-gd.x86_64   php70w-ldap.x86_64   php70w-mbstring.x86_64  php70w-mcrypt.x86_64
安装PHP FPM
yum install -y php70w-fpm

修改配置文件
--------

 - 4.1修改Nginx配置文件

nginx配置文件位置：（/etc/nginx/conf.d/default.conf）
vim /etc/nginx/conf.d/default.conf
修改 root目录,可自定义：
root   /forest/nginxDir/html;
配置php解析，修改 下面代码中黑色加粗部分：
location ~.php$ {
root   /forest/nginxDir/html; fastcgi_pass 127.0.0.1:9000; fastcgi_index index.php; fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; include    fastcgi_params; }

 - 4.2 修改php-fpm配置文件

php-fpm配置文件位置：（/etc/php-fpm.d/www.conf）
修改
user = nginx
group = nginx

启动服务
------

 - 5.1启动nginx服务：

systemctl start nginx.service
查看启动状态：
systemctl status nginx  

 - 5.2.启动PHP-FPM：

systemctl start php-fpm.service
查看启动状态：
systemctl status php-fpm.service
