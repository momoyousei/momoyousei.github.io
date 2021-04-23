---
title: 基于nginx和centos云服务搭建wordpress博客网站
date: 2021-04-04 16:02:46
tags: centos, nginx, MySQL, PHP, wordpress
---

> 前言：这是根据“云计算”这门课的作业要求，在公共云服务器上搭建一个wordpress博客网站的配置部署记录。

[TOC]



## 1. 云服务器获取

购买腾讯云的轻量级应用服务器，可以使用学生优惠套餐。系统为CentOS 7.6

## 2. Nginx安装和配置

1. 安装gcc

   若已安装，查看版本。

   ```shell
   gcc -v
   ```

   若未安装。

   ```shell
   yum install gcc
   ```

2. 安装pcre、pcre-devel

   ```shell
   yum install [-y] pcre pcre-devel
   ```

3. 安装zlib

   ```shell
   yum install [-y] zlib zlib-devel
   ```

4. 安装opensll

   ```shell
   yum install [-y] openssl openssl-devel
   ```

5. 安装nginx

   在CentOS 7中nginx提供yum源，安装方式如下。首先创建一个文件 `/etc/yum.repos.d/nginx.repo` 然后粘贴以下内容。

   ```shell
   [nginx]
   name=nginx repo
   baseurl=https://nginx.org/packages/centos/$releasever/$basearch/
   gpgcheck=0
   enabled=1
   ```

   需要手动替换 `$releasever`为 `6` 或 `7` 。运行安装命令。

   ```shell
   yum install nginx
   ```

6. 配置nginx

   启动nginx

   ```shell
   #启动nginx
   nginx    
   #停止 nginx    
   nginx -s stop    
   #从容停止 nginx
   nginx -s quit   
   #重启 nginx
   nginx -s reload  
   #查看nginx进程
   ps aux|grep nginx
   ```

   查看nginx版本

   ```shell
   nginx -v
   ```

   查看或修改nginx配置文件

   ```shell
   vim /etc/nginx/nginx.conf
   ```

## 3. MySQL安装和配置

> https://dev.mysql.com/doc/refman/8.0/en/linux-installation-yum-repo.html

1. 首先需要下载安装MySQL官网的yum repository。网址：https://dev.mysql.com/downloads/repo/yum/

   ```shell
   rpm -Uvh https://repo.mysql.com/mysql80-community-release-el7-3.noarch.rpm
   ```

2. 安装MySQL服务器

   ```shell
   yum install mysql-community-server
   ```

3. 启动MySQL

   ```shell
   systemctl start mysqld
   # 查看MySQL运行状态
   systemctl status mysqld
   # 重启服务
   systemctl restart mysqld
   # 关闭MySQL
   systemctl stop mysqld 
   ```

   > **service vs. systemctl**
   >
   > `service` operates on the files in */etc/init.d* and was used in conjunction with the old init system. `systemctl` operates on the files in */lib/systemd*. If there is a file for your service in */lib/systemd* it will use that first and if not it will fall back to the file in */etc/init.d*. Also If you are using OS like ubuntu-14.04 only service command will be available.
   >
   > So if *systemctl* is available ,it will be better to use it
   >
   > https://stackoverflow.com/questions/43537851/difference-between-systemctl-and-service-command

4. 查看root用户初始密码并重新设置密码

   ```shell
   grep "password" /var/log/mysqld.log
   # 记录最后一串初始密码
   mysql -uroot -p
   # 输入刚才记录的初始密码 myPW1234!
   ALTER USER 'root'@'localhost' IDENTIFIED BY 'myPASSWORD123!';
   ```

   > The default password policy implemented by `validate_password` requires that passwords contain at least one uppercase letter, one lowercase letter, one digit, and one special character, and that the total password length is at least 8 characters.

## 4. PHP安装和配置

1. 安装PHP

   ```shell
   yum install php-fpm php-mysql
   ```

2. 启动php-frm进程

   ```shell
   systemctl start php-fpm
   # 查看监听的端口 默认监听9000端口
   netstat -nlpt | grep php-fpm
   ```

## 5. WordPress安装和配置

1. 安装wordpress

   ```shell
   yum install wordpress
   ```

2. 配置数据库

   ```shell
   # 进入MySQL
   mysql -uroot -p
   # 在MySQL中创建wordpress库
   CREATE DATABASE wordpress;
   show databases;
   exit;
   ```

3. 在wordpress配置文件中配置数据库

   ```shell
   vim /etc/wordpress/wp-config.php
   ```

   ```php
   <?php
   /**
    * The base configuration for WordPress
    *
    * The wp-config.php creation script uses this file during the
    * installation. You don't have to use the web site, you can
    * copy this file to "wp-config.php" and fill in the values.
    *
    * This file contains the following configurations:
    *
    * * MySQL settings
    * * Secret keys
    * * Database table prefix
    * * ABSPATH
    *
    * @link https://codex.wordpress.org/Editing_wp-config.php
    *
    * @package WordPress
    */
   
   // ** MySQL settings - You can get this info from your web host ** //
   /** The name of the database for WordPress */
   define('DB_NAME', 'wordpress');
   
   /** MySQL database username */
   define('DB_USER', 'root');
   
   /** MySQL database password */
   define('DB_PASSWORD', 'myPASSWORD123!');
   
   /** MySQL hostname */
   define('DB_HOST', 'localhost');
   
   /** Database Charset to use in creating database tables. */
   define('DB_CHARSET', 'utf8');
   
   /** The Database Collate type. Don't change this if in doubt. */
   define('DB_COLLATE', '');
   
   /**#@+
    * Authentication Unique Keys and Salts.
    *
    * Change these to different unique phrases!
    * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
    * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
    *
    * @since 2.6.0
    */
   define('AUTH_KEY',         'put your unique phrase here');
   define('SECURE_AUTH_KEY',  'put your unique phrase here');
   define('LOGGED_IN_KEY',    'put your unique phrase here');
   define('NONCE_KEY',        'put your unique phrase here');
   define('AUTH_SALT',        'put your unique phrase here');
   define('SECURE_AUTH_SALT', 'put your unique phrase here');
   define('LOGGED_IN_SALT',   'put your unique phrase here');
   define('NONCE_SALT',       'put your unique phrase here');
   
   /**#@-*/
   
   /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each
    * a unique prefix. Only numbers, letters, and underscores please!
    */
   $table_prefix  = 'wp_';
   
   /**
    * See http://make.wordpress.org/core/2013/10/25/the-definitive-guide-to-disabling-auto-updates-in-wordpress-3-7
    */
   
   /* Disable all file change, as RPM base installation are read-only */
   define('DISALLOW_FILE_MODS', true);
   
   /* Disable automatic updater, in case you want to allow
      above FILE_MODS for plugins, themes, ... */
   define('AUTOMATIC_UPDATER_DISABLED', true);
   
   /* Core update is always disabled, WP_AUTO_UPDATE_CORE value is ignore */
   
   /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    *
    * For information on other constants that can be used for debugging,
    * visit the Codex.
    *
    * @link https://codex.wordpress.org/Debugging_in_WordPress
    */
   define('WP_DEBUG', false);
   
   /* That's all, stop editing! Happy blogging. */
   
   /** Absolute path to the WordPress directory. */
   if ( !defined('ABSPATH') )
       define('ABSPATH', '/usr/share/wordpress');
   
   /** Sets up WordPress vars and included files. */
   require_once(ABSPATH . 'wp-settings.php');
   ```

4. 配置Nginx

   ```shell
   # 备份Nginx设置文件
   cd /etc/nginx/conf.d/
   mv default.conf defaut.conf.bak
   # 创建 wordpress.conf 配置
   vim wordpress.conf
   ```

   ```shell
   # wordpress.conf
   server {
       listen 80;
       root /usr/share/wordpress;
       location / {
           index index.php index.html index.htm;
           try_files $uri $uri/ /index.php index.php;
       }
       # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
       location ~ .php$ {
           fastcgi_pass   127.0.0.1:9000;
           fastcgi_index  index.php;
           fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
           include        fastcgi_params;
       }
   }
   ```

   ```shell
   # 重启Nginx
   nginx -s reload  
   ```

## 6. 访问WordPress

1. 访问网址：`<yourhostip>/wp-admin/install.php` 。

