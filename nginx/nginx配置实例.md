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
