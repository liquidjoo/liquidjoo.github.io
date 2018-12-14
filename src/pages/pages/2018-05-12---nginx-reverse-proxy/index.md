---
title: nginx reverse proxy 설정
date: "2018-12-05T22:00:00.121Z"
layout: post
draft: false
path: "/posts/nginx-proxy"
category: "Dev"
description: "nginx 프록시 설정"
---

#### /etc/nginx (tree)

```
├── conf.d
│   ├── default.conf
│   └── service-url.inc
├── nginx.conf
├── sites-available
│   └── default
├── sites-enabled
│   └── default -> /etc/nginx/sites-available/default
...

```

####프록시 설정을 위한 nginx conf
virtual Host configs를 보면 include 파일이 있습니다. 위의 트리를 보면 ```/etc/nginx/conf.d/default.conf 와 sites-enable/default``` 두 파일이 있습니다.
```
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        gzip on;
        gzip_disable "msie6";

        application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}

```

####/etc/nginx/conf.d/default.conf
service-url.inc를 include하며 location / 에 해당하는 proxy 설정들 중 proxy_pass 에 해당하는 ```$service_url```의 정보가 담겨 있습니다.
```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
    include /etc/nginx/conf.d/service-url.inc;
    location / {
        proxy_pass $service_url;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
    }
    ...
    
```

####/etc/nginx/conf.d/service-url.inc
```
set $service_url http://127.0.0.1:50000;
```

위의 설정으로 보면 127.0.0.1:50000 -> 127.0.0.1:80 프록시를 해주는 것을 알 수 있습니다.