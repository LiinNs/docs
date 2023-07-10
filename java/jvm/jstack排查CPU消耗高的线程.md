# java 中快速定位正在剧烈消耗CPU的线程代码

## top

获取当前CPU消耗很高的进程PID  

ps -ef|grep PID  
看是那一个服务消耗CPU

## top -Hp PID 或者 ps -mp PID -o THREAD,tid,time

查看进程内剧烈消耗CPU的线程

获取线程NID

## printf "%X\n" 线程id

用于获取占用cpu高的线程id的16进制数，java内使用16进制  

## jstack PID > pid.log

将进程执行栈信息打印重定向到pid.log  

## less pid.log 搜索对应的16进制线程信息

https://www.jianshu.com/p/f36a1db63ad2
https://zhuanlan.zhihu.com/p/93106084
https://zhuanlan.zhihu.com/p/259834497