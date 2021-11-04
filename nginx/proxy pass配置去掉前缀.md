# proxy pass 去掉前缀

使用 Nginx 做代理的时候，可以简单的直接把请求原封不动的转发给下一个服务。

比如，访问 abc.com/appv2/a/b.html，要求转发到 localhost:8088/appv2/a/b.html

简单配置如下：

```nginx
upstream one {
  server localhost:8088 weight=5;
}

server {
    listen              80;
    server_name         abc.com;
    access_log  "pipe:rollback /data/log/nginx/access.log interval=1d baknum=7 maxsize=1G"  main;

    location / {
        proxy_set_header Host $host;
        proxy_set_header  X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://one;
    }

}
```

即，设置 proxy_pass 即可。请求只会替换域名。

但很多时候，我们需要根据 url 的前缀转发到不同的服务。

比如

abc.com/user/profile.html 转发到 用户服务 localhost:8089/profile.html

abc.com/order/details.html 转发到 订单服务 localhost:8090/details.html

即，url 的前缀对下游的服务是不需要的，除非下游服务添加 context-path，但很多时候我们并不喜欢加这个。如果 Nginx 转发的时候，把这个前缀去掉就好了。

```nginx
server {
    listen              80;
    server_name         abc.com;
    access_log  "pipe:rollback /data/log/nginx/access.log interval=1d baknum=7 maxsize=1G"  main;

    location ^~/user/ {
        proxy_set_header Host $host;
        proxy_set_header  X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://user/;
    }

    location ^~/order/ {
        proxy_set_header Host $host;
        proxy_set_header  X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://order/;
    }

}
```

^~/user/表示匹配前缀是 user 的请求，proxy_pass 的结尾有/， 则会把/user/*后面的路径直接拼接到后面，即移除 user.

另一种方案是使用 rewrite

```nginx
upstream user {
  server localhost:8089 weight=5;
}
upstream order {
  server localhost:8090 weight=5;
}


server {
    listen              80;
    server_name         abc.com;
    access_log  "pipe:rollback /data/log/nginx/access.log interval=1d baknum=7 maxsize=1G"  main;

    location ^~/user/ {
        proxy_set_header Host $host;
        proxy_set_header  X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;

        rewrite ^/user/(.*)$ /$1 break;
        proxy_pass http://user;
    }

    location ^~/order/ {
        proxy_set_header Host $host;
        proxy_set_header  X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;

        rewrite ^/order/(.*)$ /$1 break;
        proxy_pass http://order;
    }

}
```

注意到 proxy_pass 结尾没有/， rewrite 重写了 url。

```nginx
syntax: rewrite regex replacement [flag]
Default: —
Context: server, location, if
```

## references

[Nginx 代理 proxy pass 配置去除前缀](https://www.cnblogs.com/woshimrf/p/nginx-proxy-rewrite-url.html)
