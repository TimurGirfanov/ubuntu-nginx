
# Ubuntu 14.04: how to install nginx + php-fpm.

Here you can find terminal commands to install: 
* Nginx
* PHP-fpm
* curl
* MySQL
* phpmyadmin
* Memcache
* ImageMagick
* Svn
* Git
* PostgreSQL
* Composer
* PHPUnit, Codeception
* Nodejs
* Nginx config and vhosts

Launch terminal.

It's going to be much more easier to enter commands as root, so type this:
```sh
$ sudo -s
```

Install nginx
```sh
$ apt-get install nginx
```

Install PHP-fpm
```sh
$ apt-get install php5-fpm
```

Install common extensions for PHP, used by popular frameworks and libs
```sh
$ apt-get install php-pear php-apc php5-intl php5-sqlite php5-mcrypt
$ php5enmod mcrypt
```

Install cURL (used by composer)
```sh
$ apt-get install php5-curl curl libcurl3 libcurl3-dev
```

Install MySQL
```sh
$ apt-get install php5-mysql mysql-server mysql-client libmysqlclient15-dev
```

Install phpmyadmin
```sh
$ apt-get install phpmyadmin
```
```sh
ln -s /usr/share/phpmyadmin/ /usr/share/nginx/html
```

Install Memcache
```sh
$ apt-get install php5-memcache memcached
```

Install ImageMagick
```sh
$ apt-get install imagemagick php5-imagick
```

Install PostgreSQL
```sh
$ apt-get postgresql php5-pgsql
```

Install SVN and Git
```sh
$ apt-get install subversion git-core
```

Install Composer
```sh
$ curl -s https://getcomposer.org/installer | php
$ mv composer.phar /usr/local/bin/composer
```

Install PHPUnit
```sh
$ apt-get install phpunit
```

Install Codeception
```sh
$ wget http://codeception.com/codecept.phar -O /usr/local/bin/codecept
$ chmod +x /usr/local/bin/codecept
```

Install Node.js
```sh
$ apt-get install python-software-properties
$ apt-add-repository ppa:chris-lea/node.js
$ apt-get update
$ apt-get install nodejs
$ npm update -g npm
```

Nginx config, placed in: /etc/nginx/nginx.conf
```sh
timer_resolution 100ms;
worker_rlimit_nofile 8192;
worker_priority -5;

user www-data;
worker_processes 1;
pid /run/nginx.pid;

events {
    worker_connections 768;
    use epoll;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    types_hash_max_size 2048;
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    gzip on;
    gzip_disable "msie6";
    gzip_min_length 1100;
    gzip_buffers 64 8k;
    gzip_comp_level 3;
    gzip_http_version 1.1;
    gzip_proxied any;
    gzip_types text/plain application/xml application/x-javascript text/css;
    client_body_timeout 10;
    client_header_timeout 10;
    keepalive_timeout 5 5;
    send_timeout 10;
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

Nginx vhosts for "localhost" and "test.loc", placed in /etc/nginx/sites-available/default
```sh
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /usr/share/nginx/html;
    index index.html index.htm;

    server_name localhost;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}

server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80;

    server_name test.loc;
    root        /path/to/test.loc/web;
    index       index.php;

    error_log   /var/log/nginx/error_testloc.log;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
        try_files $uri =404;
    }
    error_page 404 /404.html;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }

    location ~ /\.(ht|svn|git) {
        deny all;
    }
}
```

Restart nginx
```sh
$ service nginx restart
```
