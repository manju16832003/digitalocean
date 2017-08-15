# SETUP

## Create a sudo user

### SSH to server

`ssh root@your_server_ip`

### Create new user

`adduser schumacher`

### Grant privileges to schumacher

`usermod -aG sudo schumacher`

## Install LAMP Stack

`sudo apt-get update`


### NGINX

`sudo apt-get install nginx`

Test the page visiting http://server_domain_or_IP and you would see the following page

#### Install PHP for Processing

`sudo apt-get install php-fpm php-mysql`

#### Configure the PHP Processor

Edit `sudo nano /etc/php/7.0/fpm/php.ini`

Update `cgi.fix_pathinfo=0`

Restart PHP Processor `sudo systemctl restart php7.0-fpm`

#### Configure Nginx to Use the PHP Processor

`sudo nano /etc/nginx/sites-available/default`

nginx default server block would look like

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

TO

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Test nginx config: `sudo nginx -t`

Restart: `sudo systemctl reload nginx`

#### Test PHP Page

`sudo nano /var/www/html/info.php`

```
<?php
phpinfo();
```

Test the page http://server_domain_or_IP/info.php

