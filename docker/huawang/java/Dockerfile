# 基础镜像
FROM  openjdk:8-jdk-alpine

# RUN addgroup -S spring && adduser -S spring -G spring
# USER spring:spring

# 挂载目录
VOLUME /home/ruoyi
# 创建目录
RUN mkdir -p /home/ruoyi
# 指定路径
WORKDIR /home/ruoyi
# 复制jar文件到路径
COPY ./jar/ruoyi-modules-file.jar /home/ruoyi/ruoyi-modules-file.jar

ENV JAVA_OPT=""
ENV SPRING_PROFILES_ACTIVE=prod

# 启动文件服务
ENTRYPOINT ["sh", "-c", "java ${JAVA_OPTS} -jar /app.jar"]