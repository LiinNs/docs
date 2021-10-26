# Nginx 配置实例

## nginx 针对低版本 socket 配置

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

## OPTIONS 请求优化

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

## websocket 反向代理支持

```nginx
#websocket 需要加下这个
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
```

```nginx
server
{
    listen 80;
    listen 443 ssl http2;
    server_name bgex-ow.bitdogex.com;
    index index.php index.html index.htm default.php default.htm default.html;
    root /www/wwwroot/admin.asdfl234.xyz;
    
    #SSL-START SSL 相关配置，请勿删除或修改下一行带注释的 404 规则
    #error_page 404/404.html;
    #HTTP_TO_HTTPS_START
    if ($server_port !~ 443){
        rewrite ^(/.*)$ https://$host$1 permanent;
    }
    #HTTP_TO_HTTPS_END
    ssl_certificate    /www/server/panel/vhost/cert/admin.asdfl234.xyz/fullchain.pem;
    ssl_certificate_key    /www/server/panel/vhost/cert/admin.asdfl234.xyz/privkey.pem;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    add_header Strict-Transport-Security "max-age=31536000";
    error_page 497  https://$host$request_uri;






    #SSL-END
    
    #ERROR-PAGE-START  错误页配置，可以注释、删除或修改
    #error_page 404 /404.html;
    #error_page 502 /502.html;
    #ERROR-PAGE-END
    
    #PHP-INFO-START  PHP 引用配置，可以注释或修改
    include enable-php-00.conf;
    #PHP-INFO-END
    
    #REWRITE-START URL 重写规则引用，修改后将导致面板设置的伪静态规则失效
    include /www/server/panel/vhost/rewrite/admin.asdfl234.xyz.conf;
    #REWRITE-END
    
    #禁止访问的文件或目录
    location ~ ^/(\.user.ini|\.htaccess|\.git|\.svn|\.project|LICENSE|README.md)
    {
        return 404;
    }
    
    #一键申请 SSL 证书验证目录相关设置
    location ~ \.well-known{
        allow all;
    }
    
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
    {
        expires      30d;
        error_log /dev/null;
        access_log /dev/null;
    }
    
    location ~ .*\.(js|css)?$
    {
        expires      12h;
        error_log /dev/null;
        access_log /dev/null; 
    }
    access_log  /www/wwwlogs/admin.asdfl234.xyz.log;
    error_log  /www/wwwlogs/admin.asdfl234.xyz.error.log;
}
```
