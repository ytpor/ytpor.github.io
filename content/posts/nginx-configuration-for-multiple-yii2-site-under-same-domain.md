---
title: 'Nginx Configuration for Multiple Yii2 Site Under Same Domain'
date: 2017-12-27T16:03:00+08:00
draft: false
url: /2017/12/nginx-configuration-for-multiple-yii2.html
tags:
- nginx
- yii2
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/c/c5/Nginx_logo.svg"
  alt: "Nginx"
  relative: false
---

This nginx configuration will allow you to run multiple instances of Yii2 in sub folders under the same domain. Eg. http://advanced.local/ and http://advanced.local/subfolder/

```
server {
    listen       80; # listen for IPv4
    server_name  advanced.local;

    set $root_path /var/www/main/web;
    root        $root_path;
    index       index.html index.php;

    charset      utf-8;
    client_max_body_size  100M;

    location / {
        root  $root_path;
        try_files  $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files  $uri /$uri =404;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        include fastcgi_params;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ^~ /subfolder {
        alias /var/www/subfolder/web;

        if (!-e $request_filename) { rewrite ^ /subfolder/index.php last; }

        location ~ \.php$ {
            if (!-f $request_filename) { return 404; }

            include        fastcgi_params;
            fastcgi_param  SCRIPT_FILENAME $request_filename;
            fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }
    }

    location ~* \.(htaccess|htpasswd|svn|git) {
        deny all;
    }
}
```