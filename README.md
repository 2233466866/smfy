## 介绍

1. 基于symfony框架的API项目

1. Nginx
   ```
   # PHP-FPM
   # https://symfony.com/doc/3.4/setup/web_server_configuration.html#web-server-nginx
   server {
       listen       80;
       server_name  www.xxx.com;
       root         /xxx/yuelu_api/web;
   
       location / {
           try_files $uri /app.php$is_args$args;
       }
   
       # DEV
       location ~ ^/(app_dev|config)\.php(/|$) {
           fastcgi_pass             127.0.0.1:9000;
           fastcgi_split_path_info  ^(.+\.php)(/.*)$;
           include                  fastcgi_params;
           fastcgi_param            SCRIPT_FILENAME     $realpath_root$fastcgi_script_name;
           fastcgi_param            DOCUMENT_ROOT       $realpath_root;
       }
   
       # PROD
       location ~ ^/app\.php(/|$) {
           fastcgi_pass             127.0.0.1:9000;
           fastcgi_split_path_info  ^(.+\.php)(/.*)$;
           include                  fastcgi_params;
           fastcgi_param            SCRIPT_FILENAME     $realpath_root$fastcgi_script_name;
           fastcgi_param            DOCUMENT_ROOT       $realpath_root;
           internal;
       }
   
       location ~ \.php$ {
           return 404;
       }
   
       error_log    /xxx/error.log;
       access_log   /xxx/access.log;
   }
   ```

1. Apache
   ```
   # mod_php/PHP-CGI
   # https://symfony.com/doc/3.4/setup/web_server_configuration.html#apache-with-mod-php-php-cgi
   <VirtualHost *:80>
       ServerName   xxx.com
       ServerAlias  www.xxx.com
   
       DocumentRoot /xxx/yuelu_api/web
       <Directory /xxx/yuelu_api/web>
           AllowOverride All
           Order Allow,Deny
           Allow from All
       </Directory>
   
       # <Directory /var/www/project>
       #     Options FollowSymlinks
       # </Directory>
   
       ErrorLog     /xxx/error.log
       CustomLog    /xxx/access.log combined
   </VirtualHost>
   
   # PHP-FPM
   # https://symfony.com/doc/3.4/setup/web_server_configuration.html#web-server-apache-fpm
   <VirtualHost *:80>
       ServerName   xxx.com
       ServerAlias  www.xxx.com
   
       AddHandler   php7-fcgi .php
       Action       php7-fcgi /php7-fcgi
       Alias        /php7-fcgi /usr/lib/cgi-bin/php7-fcgi
       FastCgiExternalServer /usr/lib/cgi-bin/php7-fcgi -host 127.0.0.1:9000 -pass-header Authorization
   
       DocumentRoot /xxx/yuelu_api/web
       <Directory /xxx/yuelu_api/web>
           AllowOverride All
           Order Allow,Deny
           Allow from all
       </Directory>
   
       # <Directory /var/www/project>
       #     Options FollowSymlinks
       # </Directory>
   
       ErrorLog     /xxx/error.log
       CustomLog    /xxx/access.log combined
   </VirtualHost>
   ```
