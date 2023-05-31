# Webserver

## Structure

Our directory structure will be as such :
```bash
docker-compose.yml
Dockerfile
websites # All php files. heart of the application
nginx # Configuration of your nginx and all of your subdomain
``` 

## Using docker and conf

let's set up our docker-compose.yml.

```yaml
version: "3"
services:

  # PHP service
  app:
      build: .
      container_name: php-app
      working_dir: /var/www/
      volumes:
        - ./websites:/var/www
      networks:
        - app-network

  # our server
  nginx:
      image: nginx:latest
      volumes:
        - ./websites:/var/www
        - ./nginx:/etc/nginx/conf.d
      ports:
        - "80:80"
      networks:
        - app-network

  # MySQL database service
  db:
    image: hypriot/rpi-mysql:latest
    container_name: mysql-db
    ports:
        - "3306:3306"
    environment:
        MYSQL_ROOT_PASSWORD: "password"
        MYSQL_DATABASE: db
        MYSQL_USER: user
        MYSQL_PASSWORD: "password"
    networks:
        - app-network

networks:
  app-network:
      driver: bridge
```

It is important the the volumes in nginx and app are the same.

You will need a Dockerfile for your php container

```bash

FROM php:8-fpm-alpine

# Install system dependencies
# RUN apt-get update && apt-get install -y git

# Install PHP extensions
RUN docker-php-ext-install pdo pdo_mysql

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www
```

let's create the nginx.conf for one of the subdomain.  
each subdomain will need to follow the same naming rules `nginx/games.web.local.conf` and should look like this :

```bash

server {
    ## Listen for ipv4;
    listen 80;
    ## Listen for ipv6;
    listen [::]:80 default ipv6only=on;

    root /var/www/games;
    index index.php index.html index.htm;

    ## Make site accessible from http://localhost
    server_name games.web.local;

    # Disable sendfile as per https://docs.vagrantup.com/v2/synced-folders/virtualbox.html
    sendfile off;

    # Add stdout logging
    error_log /dev/stdout info;
    access_log /dev/stdout;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to index.html
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
    location = /404.html {
        root /var/www/errors;
        internal;
    }

    # Pass the PHP scripts to FastCGI server listening on socket
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php-app:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param SCRIPT_NAME $fastcgi_script_name;
        fastcgi_index index.php;
        include fastcgi_params;
    }

    # Deny access to . files, for security
    location ~ /\. {
            log_not_found off;
            deny all;
    }

    location ^~ /.well-known {
            allow all;
            auth_basic off;
    }
}
``` 
