# GC

## 垃圾回收日志

JVM 参数

`-XX:+HeapDumpOnOutOfMemoryError -XX:+PrintGCDateStamps  -XX:+PrintGCDetails -Xloggc:./logs/gc-%t.log`

## GC 日志分析

1. 在线分析

    [gceasy.io](https://gceasy.io/)

2. 离线分析

    [GCViewer](https://github.com/chewiebug/GCViewer)

## GC 日志分片

1. 启动时添加后缀 `-XX:+PrintGCDateStamps  -XX:+PrintGCDetails -Xloggc:./logs/gc-%t.log`

2. JVM 参数自动分片 `-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:/logs/gc.log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=2M`

两种方式的优缺点 文件大小 新日志 旧日志 重启

## reference

[ROTATING GC LOG FILES](https://blog.gceasy.io/2016/11/15/rotating-gc-log-files/)
