# Redis 在 CentOS 下的自启动

## Redis 的后台运行

1. 命令行使用 --daemonize 启动 ```/usr/local/sbin/redis-server --daemonize /usr/local/redis/redis.conf```
2. 修改配置文件内的 ```deaminize yes```

## Redis 配置自启动

```vim /etc/systemd/system/redis.service```

```redis.service
[Unit]
Description=The Redis server
After=network.target

[Service]
Type=forking
PIDFile=/var/run/redis_6379.pid
ExecStart=/usr/local/bin/redis-server /usr/local/redis/redis-6.2.6/pro-redis.conf
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target

#重新加载配置文件
systemctl daemon-reload
#启动服务
systemctl start redis.service
#开机启动
systemctl enable redis.service
```

## reference

[Redis 篇 - 安装和开机启动](https://zhuanlan.zhihu.com/p/149815328)
