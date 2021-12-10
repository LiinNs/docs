# docker 常见问题处理

## 容器时区处理

1. Dockerfile 中创建时区文件

    ```Dockerfile
    FROM centos
    RUN rm -f /etc/localtime \
    && ln -sv /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone
    ```

2. 挂载主机时区配置到容器内

    a. 挂载本地 `/etc/localtime`
    `docker run -it -v /etc/localtime:/etc/localtime centos /bin/sh`  

    b. 挂载本地 `/usr/share/zoneinfo/Asia/Shanghai`
    `docker run -it -v /usr/share/zoneinfo/Asia/Shanghai:/etc/localtime centos /bin/sh`

## reference

[解决容器内时区不一致问题](https://cloud.tencent.com/document/product/457/41877#mount)
