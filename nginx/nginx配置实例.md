# Nginx配置实例

## nginx针对低版本socket配置

```conf
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

## ssl 配置

```conf
ssl_certificate    /www/server/panel/vhost/cert/SITE/fullchain.pem;
ssl_certificate_key    /www/server/panel/vhost/cert/SITE/privkey.pem;
ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
ssl_ciphers XXX;
ssl_prefer_server_ciphers on;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

## OPTIONS请求优化

```conf
if ($request_method = 'OPTIONS') {
  add_header 'Access-Control-Allow-Origin' "$http_origin";
  add_header 'Access-Control-Allow-Methods' '*';
  add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type'; 
  add_header 'Access-Control-Allow-Credentials' true;
  add_header 'Access-Control-Max-Age' 86400;
  return 204; 
}
```

```conf
user  nginx;
worker_processes  16;
worker_rlimit_nofile 100000;

error_log  /var/log/nginx/error.log;
pid        /var/run/nginx.pid;

events {
    worker_connections  10240;
    use epoll;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  /var/log/nginx/access.log  main;
    access_log  off;

    sendfile        on;
    tcp_nopush     on;
    keepalive_timeout  30;
    reset_timedout_connection on;
    tcp_nodelay on;
    server_tokens off;

    gzip  on;
    gzip_http_version 1.1;
    gzip_vary on;
    gzip_comp_level 6;
    gzip_proxied any;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_buffers 16 8k;

    charset utf-8;

    upstream php1  {
      server   unix:/dev/shm/.php1.socket;
    }
    upstream php2  {
      server   unix:/dev/shm/.php2.socket;
    }
    upstream php3  {
      server   unix:/dev/shm/.php3.socket;
    }

    ssl_certificate     /etc/pki/tls/certs/site.com.crt;
    ssl_certificate_key /etc/pki/tls/certs/site.com.key;
    ssl_ciphers         RC4-SHA:AES128-SHA:AES256-SHA:CAMELLIA128-SHA:HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;


    limit_req_zone  $binary_remote_addr  zone=one:10m   rate=1r/s;
    limit_req_zone  $uri  zone=two:10m   rate=200r/s;

    include /etc/nginx/conf.d/*.conf;
}
```
