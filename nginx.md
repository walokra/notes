# Nginx

## Proxy pass

```
user www-data;
worker_processes 4;
error_log  /var/log/nginx/error.log warn;
pid /run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;

    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                     '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;

    upstream foo_service {
		server localhost:8484;
    }

    server {
        listen         80;
        server_name    localhost_nginx

        proxy_buffers 8 16k;
        proxy_buffer_size 32k;
        proxy_set_header Host $host;

        location /foo {
            rewrite ^(.*[^/])$ $1/ redirect;
        }
        location /foo/ {
            proxy_cookie_path / /foo/;
            proxy_pass http://foo_service/;
            proxy_redirect default;
        }
    }
}
```
