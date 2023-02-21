---
date created: 2022-08-16
date modified: 2022-02-14
tags: [deploy, backup, newdawn]
title: 🎡 DEPLOY UNITY WEBGL ASSETS ON NGINX
---
## REFERENCE
[Server configuration code samples - Unity 手册](https://docs.unity3d.com/cn/2022.1/Manual/webgl-server-configuration-code-samples.html)
## TOOLS
[NGINX Beautifier](https://nginxbeautifier.github.io) - Nginx configuration formatter online
## Docker docker-compose config.
```YAML
services:
  web:
    image: nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./www/:/usr/share/nginx/html
    ports:
      - "8081:80"
```
## A simple nginx.conf file.
```nginx
user nginx;
worker_processes auto;

error_log /var/log/nginx/error.log notice;
pid /var/run/nginx.pid;


events
{
        worker_connections 1024;
}


http
{
        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        log_format main '$remote_addr - $remote_user [$time_local] "$request" '
        '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" "$http_x_forwarded_for"';

        access_log /var/log/nginx/access.log main;

        sendfile on;
        #tcp_nopush     on;

        keepalive_timeout 65;

        gzip off;

        #include /etc/nginx/conf.d/*.conf;

        server
        {
                listen 80;

                root /usr/share/nginx/html;
                #try_files $uri /index.html;

                location ~ .+\.(data|symbols\.json)\.br$
                {
                        gzip off;
                        add_header Content-Encoding br;
                        default_type application/octet-stream;
                }

                location ~ .+\.js\.br$
                {
                        gzip off; # Do not attempt dynamic gzip compression on an already compressed file
                        add_header Content-Encoding br;
                        default_type application/javascript;
                }

                location ~ .+\.wasm\.br$
                {
                        gzip off; # Do not attempt dynamic gzip compression on an already compressed file
                        add_header Content-Encoding br;
                        default_type application/wasm;
                }

                location ~ .+\.(data|symbols\.json)\.gz$
                {
                        gzip off; # Do not attempt dynamic gzip compression on an already compressed file
                        add_header Content-Encoding gzip;
                        default_type application/octet-stream;
                }

                location ~ .+\.js\.gz$
                {
                        gzip off; # Do not attempt dynamic gzip compression on an already compressed file
                        add_header Content-Encoding gzip;
                        default_type application/javascript;
                }

                location ~ .+\.wasm\.gz$
                {
                        gzip off; # Do not attempt dynamic gzip compression on an already compressed file
                        add_header Content-Encoding gzip;
                        default_type application/wasm;
                }
        }
}
```