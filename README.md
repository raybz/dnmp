1. 结构
www
|— docker-compose.yml
|— html
|    |— project1
|        |— index.php
|        |— index.html
|— nginx
|    |—  default.conf
|— php
|    |— php.init
|    |— www.conf
|— mysql
|— .env

2. .env
```
#!/usr/bin/env bash
#Nginx
NGINX_VERSION=latest
NGINX_HOST=localhost
HOME=/Users/yourHome

# Mysql
MYSQL_VERSION=latest
MYSQL_ROOT_USER=root
MYSQL_ROOT_PASSWORD=123456
MYSQL_USER=dev
MYSQL_PASSWORD=123456

#PHP
PHP_VERSION='7.1.19-fpm'
```

3. docker-compose.yml
```
version: "3"
services:
php:
container_name: php-fpm
image: php:7.1-fpm
ports:
- 9000:9000
networks:
- front
restart: always
volumes:
- ${HOME}/www/php/php.ini:/usr/local/etc/php/conf.d/php.ini
- ${HOME}/www/php/www.conf:/usr/local/etc/php-fpm.d/www.conf
- ${HOME}/www/html:/usr/share/nginx/html
- ${HOME}/www/html:/var/www/html
nginx:
container_name: nginx
image: nginx:latest
ports:
- 80:80
networks:
- front
env_file:
- .env
environment:
- NGINX_HOST=${NGINX_HOST}
depends_on:
- php
- mysql
volumes:
- ${HOME}/www/nginx/default.conf:/etc/nginx/conf.d/default.conf
- ${HOME}/www/nginx:/etc/nginx/conf.d
- ${HOME}/www/html:/usr/share/nginx/html
restart: always
mysql:
container_name: mysql
image: mysql:latest
ports:
- 3306:3306
env_file:
- .env
environment:
- MYSQL_ROOT_USER=${MYSQL_ROOT_USER}
- MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
networks:
- front
volumes:
- ${HOME}/www/mysql:/var/lib/mysql
restart: always
# command: ['']
redis:
container_name: redis
image: redis:latest
ports:
- 6379:6379
networks:
- front
restart: always
depends_on:
- php
networks:
front:
driver: bridge
```