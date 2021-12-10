# https://mherman.org/blog/dockerizing-a-vue-app/

# build stage
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# production stage
FROM openjdk:8-jdk as production-stage
COPY *.jar /app/*.jar
# 日志 挂载
EXPOSE 8764
ENTRYPOINT ["java","-jar","ruoyi-modules-job.jar"]

# !/bin/bash
# npm run build:production

# docker run -di --name nginx -p 80:80 -v /data/h5/nginx/conf:/etc/nginx/conf.d nginx

# java项目的docker file
# deploy 脚本
# nginx 配置 前端项目 域名 ssl证书
# mysql docker 数据库初始化脚本
# redis docker
# 安装docker
# 在服务器上面配置部署脚本