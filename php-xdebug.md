# PHP and XDebug

webgrind: <https://github.com/jokkedk/webgrind>
xdebug: <https://xdebug.org/>

## Configuration

### PHP

Add to your php.ini

```ini
[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
xdebug.remote_port = 9000
xdebug.remote_handler = dbgp
xdebug.remote_host = host.docker.internal
xdebug.idekey = foobar
xdebug.trace_enable_trigger = 1
xdebug.profiler_enable = 1
xdebug.profiler_output_dir = "/tmp/"
xdebug.profiler_output_name = "cachegrind.out.%t-%s"
xdebug.trace_output_name = "trace.%S"
xdebug.profiler_append = 1
xdebug.profiler_enable_trigger = 1
xdebug.trace_output_dir = "/tmp/"
xdebug.remote_mode = req
xdebug.remote_log = /tmp/xdebug_remote.log
xdebug.remote_connect_back = 0
```

*www.conf*

```
[www]

listen = 9000
```

*Dockerfile*

```
FROM php:7.4-fpm-alpine

RUN set -ex \
    && apk --update add $PHPIZE_DEPS postgresql-dev \
    && docker-php-ext-install pdo pdo_pgsql \
    && pecl install xdebug-2.9.6 redis \
    && docker-php-ext-enable xdebug redis \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && apk del $PHPIZE_DEPS

COPY ./docker/php/www.conf /etc/php-fpm.d/www.conf
COPY ./docker/php/php.ini /usr/local/etc/php/php.ini

EXPOSE 9000
```

### nginx

*Dockerfile*

```
FROM nginx:alpine

COPY ./docker/nginx/nginx.conf /etc/nginx/nginx.conf

EXPOSE 80
```

*nginx.conf*

```
worker_processes      1;

events {
  worker_connections    1024;
}

http {
  server {
    listen                  80 default_server;
    server_name             _;

    access_log              /var/log/nginx/access.log;
    error_log               /var/log/nginx/error.log;

    root                    /var/www/html/public;

    location / {
      try_files               $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
      include                 /etc/nginx/fastcgi_params;
      fastcgi_pass            php:9000;
      fastcgi_index           index.php;

      fastcgi_param           SCRIPT_FILENAME

      $document_root$fastcgi_script_name;
    }
  }
}
```


### Docker

*docker-compose.yml*

```
version: "3"

services:
  nginx:
    container_name: nginx-fpm
    build:
      context: .
      dockerfile: ./docker/nginx/Dockerfile
    restart: always
    ports:
      - "3331:80"
    depends_on:
      - php
    networks:
      default:
      nginx-proxy:
      discovery:
        aliases:
          - myservice

  webgrind:
    image: jokkedk/webgrind
    ports:
      - 3050:80
    volumes:
      - xdebug:/tmp/
    networks:
      - default

  php:
    build:
      context: .
      dockerfile: ./docker/php/Dockerfile
    restart: always
    volumes:
      - .:/var/www/html
      - xdebug:/tmp/
    networks:
      - default

networks:
  default:
  nginx-proxy:
    external:
      name: nginx-proxy
  discovery:
    external:
      name: discovery

volumes:
  xdebug:
```

### Install VS Code extension

PHP Debug Adapter for Visual Studio Code
felixfbecker.php-debug

Read more: 
* <https://dev.to/zeegcl/debugging-a-php-project-on-vscode-with-xdebug-2anp>
* <https://www.cloudways.com/blog/php-debug/>


    On the left side menu go to "Run".
    At the top of the sidebar, if it says "No configurations", open the dropdown and choose "Add configuration".
    In the "Select environment" prompt, choose "PHP".
    Adjust the parameters according to the way you configured Xdebug in php.ini.

To add breakpoints, just click in the blank space to the left of the line numbers in the editor pane. 
A red dot will appear. After that, click the green play button in the sidebar; 
Now the bottom bar should change from blue to orange, signaling that it started in debug mode.

If you execute your code, the runtime will stop at the breakpoint and the IDE will allow you to inspect the 
variables at that moment in time, or use the step buttons to see what happens after that breakpoint. 
To continue executing your code, just press the play button again, or stop with the stop button (red square).
