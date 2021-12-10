CD 项目脚本

```sh
drwxr-xr-x.  3 root root      4096 Dec  2 11:31 .
drwxr-xr-x  23 root root      4096 Dec  1 01:50 ..
-rw-r--r--.  1 root root       344 Oct 12  2020 apiinstall.sh
-rw-r--r--   1 root root    141893 Sep  3 11:45 arthas-boot.jar
-rw-r--r--   1 root root    141893 Sep  3 11:45 arthas-boot.jar.1
-rw-r--r--   1 root root        91 Jul 22 16:04 ba?scene=ba=rc007&store=6997&page=pages%2Fhome%2Findex
-rw-r--r--   1 root root        91 Jul 22 16:03 ba?scene=url=https:%2F%2Ft-sephora.imageco.cn%2Ft%2Fb4EEvKUX&page=sp%2Fweb
-rw-r--r--   1 root root        91 Jul 22 16:04 ba?scene=url=https:%2F%2Ft-sephora.imageco.cn%2Ft%2Fb4EEvKUX&page=sp%2Fweb.1
-rw-r--r--   1 root root 106486970 Dec  2 11:30 basic-service-1.1.0.jar
-rw-r--r--.  1 root root       361 Nov 11  2020 check.sh
-rw-r--r--.  1 root root       837 Sep 15  2020 configs.conf
-rw-r--r--.  1 root root       202 Oct 21  2020 cp_jar.sh
drwxr-xr-x.  4 root root      4096 Dec  2 12:00 getsftp
-rw-r--r--.  1 root root      3071 Sep 15  2020 intcfg.sh
-rw-r--r--.  1 root root       158 Oct 21  2020 int.conf
-rw-r--r--.  1 root root      1533 Nov  9  2020 mvn.sh
-rw-r--r--   1 root root  77841339 Dec  1 23:05 order-service-1.1.0.jar
-rw-r--r--   1 root root  77915890 Dec  1 17:55 product-service-1.1.0.jar
-rw-r--r--.  1 root root       284 Oct 21  2020 runlist
-rw-r--r--.  1 root root      3673 Nov 11  2020 run.sh
-rw-r--r--   1 root root  80321831 Dec  1 23:04 sales-service-1.1.0.jar
-rw-r--r--   1 root root  49156082 Dec  1 17:55 system-gateway-1.1.0.jar
-rw-r--r--   1 root root  79286143 Dec  2 11:09 user-service-1.1.0.jar
```

```bash
#!/bin/bash
servicelist="common-core system-gateway product-service order-service sales-service user-service basic-service"
app_path=/opt/applications
runopt=$1

while read line;do
    eval "$line"
done < int.conf

echo -n '请输入 git user:'
read gituser

echo -n '请输入 git password:'
read gitpasswd

case "$runopt" in
  "all")

      for i in $servicelist
      do
          cd ${app_path}/${i}

/usr/bin/expect <<-EOF
set timeout -1
spawn git pull origin $INT_BRANCH
expect {
    "Username" { send "$gituser\r"; exp_continue }
    "Password" { send "$gitpasswd\r" }
}
expect eof
EOF

          mvn clean install -Dmaven.test.skip=true -U
          echo '......'
          sleep 3

      done
      ;;
  *)
      if [ "$app_path/$runopt" = "/opt/applications/" ];then
          echo "Please input \$1 with mvn.sh"
          echo "(common-core | system-gateway | product-service | order-service | sales-service | user-service | basic-service | all)"
          exit 100
      fi
      if [ -d $app_path/$runopt ];then
          cd $app_path/$runopt
/usr/bin/expect <<-EOF
set timeout -1
spawn git pull origin $INT_BRANCH
expect {
    "Username" { send "$gituser\r"; exp_continue }
    "Password" { send "$gitpasswd\r" }
}
expect eof
EOF
          mvn clean install -Dmaven.test.skip=true -U
      else
          echo "$app_path/$runopt not exists!!"
          echo "(common-core | system-gateway | product-service | order-service | sales-service | user-service | basic-service | all)"
          exit 100
      fi
      ;;
esac
```

```bash
#!/bin/bash

for i in $(cat runlist)
do
    service=`echo $i | awk -F ':' '{print $1}'`
    port=`echo $i | awk -F ':' '{print $2}'`
    netstat -nuplt | grep $port |grep -v grep
    if [ $? -eq 0 ];then
        echo -e "$service:                    \033[32m ON \033[0m"
    else
        echo -e "$service:                    \033[31m OFF \033[0m"
    fi
done
```

```init
order-service:10204:-Xms512m:-Xmx512m:-Xmn128m
product-service:10203:-Xms512m:-Xmx512m:-Xmn128m
user-service:10202:-Xms512m:-Xmx512m:-Xmn128m
sales-service:10207:-Xms512m:-Xmx512m:-Xmn128m
basic-service:10208:-Xms512m:-Xmx512m:-Xmn128m
system-gateway:10101:-Xms512m:-Xmx512m:-Xmn128m
```

```conf
CFG_MAIN_DOMAIN=sephora-uat.icaodong.com
CFG_MAIN_HTTPORS=https
CFG_QINIU_DOMAIN=cdqn.icaodong.com
CFG_QINIU_ACCESS_KEY=xxxxxx
CFG_QINIU_SECRET_KEY=xxxxxx
CFG_QINIU_BK_NAME=xxxxxx
CFG_XXL_JOB_SERVER=127.0.0.1:10010
CFG_REDIS_HOST=127.0.0.1
CFG_REDIS_PORT=6379
CFG_REDIS_PWD=kdK38Al39C093kjdfkladhf8923
CFG_DISTRIBUTION_SERVER=127.0.0.1:13001
CFG_WX_COMPONENT=xxxxxx
CFG_RABBITMQ_HOST=127.0.0.1
CFG_RABBITMQ_PORT=5672
CFG_RABBITMQ_VHOST=cdmall-sephora-uat
CFG_RABBITMQ_USER=admin
CFG_RABBITMQ_PWD=ilovecaodong
CFG_MYSQL_W_HOST=10.157.53.7
CFG_MYSQL_W_PORT=3306
CFG_MYSQL_R_HOST=10.157.53.7
CFG_MYSQL_R_PORT=3306
CFG_MYSQL_CD_JOB_PWD=xxxxxxxx
CFG_MYSQL_CD_ORDER_PWD=xxxxxxxx
CFG_MYSQL_CD_SALES_PWD=xxxxxxxx
CFG_MYSQL_CD_USER_PWD=xxxxxxxx
CFG_MYSQL_CD_PRODUCT_PWD=xxxxxxxx
CFG_MYSQL_CD_SYSTEM_PWD=xxxxxxxx
CFG_MYSQL_CDMALL_DIS_PWD=xxxxxxxx
```

```bash inicfg
#!/bin/bash
while read line;do
    eval "$line"
done < configs.conf

configlist="job-service-config miniapp-bff-config order-service-config sales-service-config user-service-config manager-bff-config open-api-config product-service-config system-service-config wechat-service-config"

for i in $configlist
do
    cd /opt/configs/$i
    /usr/bin/cp application-template.yml application-tmp.yml
    sed -i "s/CFG_MAIN_DOMAIN/$CFG_MAIN_DOMAIN/g" application-tmp.yml
    sed -i "s/CFG_MAIN_HTTPORS/$CFG_MAIN_HTTPORS/g" application-tmp.yml
    sed -i "s/CFG_QINIU_DOMAIN/$CFG_QINIU_DOMAIN/g" application-tmp.yml
    sed -i "s/CFG_QINIU_ACCESS_KEY/$CFG_QINIU_ACCESS_KEY/g" application-tmp.yml
    sed -i "s/CFG_QINIU_SECRET_KEY/$CFG_QINIU_SECRET_KEY/g" application-tmp.yml
    sed -i "s/CFG_QINIU_BK_NAME/$CFG_QINIU_BK_NAME/g" application-tmp.yml
    sed -i "s/CFG_XXL_JOB_SERVER/$CFG_XXL_JOB_SERVER/g" application-tmp.yml
    sed -i "s/CFG_REDIS_HOST/$CFG_REDIS_HOST/g" application-tmp.yml
    sed -i "s/CFG_REDIS_PORT/$CFG_REDIS_PORT/g" application-tmp.yml
    sed -i "s/CFG_REDIS_PWD/$CFG_REDIS_PWD/g" application-tmp.yml
    sed -i "s/CFG_DISTRIBUTION_SERVER/$CFG_DISTRIBUTION_SERVER/g" application-tmp.yml
    sed -i "s/CFG_WX_COMPONENT/$CFG_WX_COMPONENT/g" application-tmp.yml
    sed -i "s/CFG_RABBITMQ_HOST/$CFG_RABBITMQ_HOST/g" application-tmp.yml
    sed -i "s/CFG_RABBITMQ_PORT/$CFG_RABBITMQ_PORT/g" application-tmp.yml
    sed -i "s/CFG_RABBITMQ_VHOST/$CFG_RABBITMQ_VHOST/g" application-tmp.yml
    sed -i "s/CFG_RABBITMQ_USER/$CFG_RABBITMQ_USER/g" application-tmp.yml
    sed -i "s/CFG_RABBITMQ_PWD/$CFG_RABBITMQ_PWD/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_W_HOST/$CFG_MYSQL_W_HOST/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_W_PORT/$CFG_MYSQL_W_PORT/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_R_HOST/$CFG_MYSQL_R_HOST/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_R_PORT/$CFG_MYSQL_R_PORT/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_CD_ORDER_PWD/$CFG_MYSQL_CD_ORDER_PWD/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_CD_SALES_PWD/$CFG_MYSQL_CD_SALES_PWD/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_CD_USER_PWD/$CFG_MYSQL_CD_USER_PWD/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_CD_PRODUCT_PWD/$CFG_MYSQL_CD_PRODUCT_PWD/g" application-tmp.yml
    sed -i "s/CFG_MYSQL_CD_SYSTEM_PWD/$CFG_MYSQL_CD_SYSTEM_PWD/g" application-tmp.yml
    /usr/bin/cp application-tmp.yml application-prod.yml
done

cd /opt/applications/cdmall-distribution/src/main/resources
/usr/bin/cp application.properties.template application.properties.tmp
sed -i "s/CFG_REDIS_HOST/$CFG_REDIS_HOST/g" application.properties.tmp
sed -i "s/CFG_REDIS_PORT/$CFG_REDIS_PORT/g" application.properties.tmp
sed -i "s/CFG_REDIS_PWD/$CFG_REDIS_PWD/g" application.properties.tmp
sed -i "s/CFG_MYSQL_W_HOST/$CFG_MYSQL_W_HOST/g" application.properties.tmp
sed -i "s/CFG_MYSQL_W_PORT/$CFG_MYSQL_W_PORT/g" application.properties.tmp
sed -i "s/CFG_MYSQL_CDMALL_DIS_PWD/$CFG_MYSQL_CDMALL_DIS_PWD/g" application.properties.tmp
/usr/bin/cp application.properties.tmp application.properties
```

```bash run.sh
#!/bin/bash

options=$1
service=$2
env="uat"

startFunc(){
    echo "Starting $1 ......"
    if [ "$1" == "cdmall-distribution" ];then
        if [ ! -d /opt/app_logs/cdmall-distribution ];then
            mkdir -p /opt/app_logs/cdmall-distribution
        fi
        nohup java -jar $3 $4 -Dserver.port=$2 cdmall-distribution.jar > /opt/app_logs/cdmall-distribution/run.log & 2>&1 &
    else
        nohup java -jar $3 $4 $5 -XX:+UseParallelOldGC -XX:-PrintGC -XX:-PrintGCTimeStamps -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/heap.dump-$1 -Dspring.profiles.active=${env} -Dspring.config.location=/opt/configs/$1/application-${env}.yml -Dserver.port=$2 ./$1-1.1.0.jar >/dev/null & 2>&1 &
    fi
}

stopFunc(){
    echo "Stoping $1 ."
    if [ "$1" == "cdmall-distribution" ];then
        ps aux | grep 'cdmall-distribution.jar' | grep -v grep > /dev/null 2>&1
        if [ $? -eq 0 ];then
            killid=`ps aux | grep 'cdmall-distribution.jar' | grep -v grep | awk '{print $2}' | head -1`
            kill -9 $killid
            sleep 1
        fi
        pdval=true
        while $pdval;
        do
            ps aux | grep 'cdmall-distribution.jar' | grep -v grep > /dev/null
            if [ $? -eq 0 ];then
                echo -n '.'
                sleep 1
            else
                pdval=false
            fi
        done
    else
        ps aux | grep "$1-1.1.0.jar" | grep -v grep > /dev/null 2>&1
        if [ $? -eq 0 ];then
            killid=`ps aux | grep "$1-1.1.0.jar" | grep -v grep | awk '{print $2}' | head -1`
            kill -9 $killid
            sleep 1
        fi
        pdval=true
        while $pdval;
        do
            ps aux | grep "$1-1.1.0.jar" | grep -v grep > /dev/null 2>&1
            if [ $? -eq 0 ];then
                echo -n '.'
                sleep 1
            else
                pdval=false
            fi
        done
    fi
}

runOne(){
    isin=false
    for i in `cat runlist`
    do
        service_name=`echo $i | awk -F ':' '{print $1}'`
        if [ "$2" == "$service_name" ];then
            isin=true
            if [ "$1" == "start" ];then
                service_port=`echo $i | awk -F ':' '{print $2}'`
                jaropt_1=`echo $i | awk -F ':' '{print $3}'`
                jaropt_2=`echo $i | awk -F ':' '{print $4}'`
                jaropt_3=`echo $i | awk -F ':' '{print $5}'`
                startFunc $service_name $service_port $jaropt_1 $jaropt_2 $jaropt_3
                break
            else
                stopFunc $service_name
            fi
        fi
    done
    if [ ! $isin ];then
        echo "Unknown service $service"
    fi
}

runAll(){
    for i in `cat runlist`
    do
        service_name=`echo $i | awk -F ':' '{print $1}'`
        if [ "$1" == 'start' ];then
        service_port=`echo $i | awk -F ':' '{print $2}'`
        jaropt_1=`echo $i | awk -F ':' '{print $3}'`
        jaropt_2=`echo $i | awk -F ':' '{print $4}'`
        jaropt_3=`echo $i | awk -F ':' '{print $5}'`
        startFunc $service_name $service_port $jaropt_1 $jaropt_2 $jaropt_3
        else
        stopFunc $service_name
        fi
    done
}

case $options in
  "start")
    if [ "$service" == "all" ];then
        runAll start
    else
        runOne start $service
    fi
    ;;
  "stop")
    if [ "$service" == "all" ];then
        runAll stop
    else
        runOne stop $service
    fi
    ;;
  "restart")
    if [ "$service" == "all" ];then
        runAll stop
        runAll start
    else
        runOne stop $service
        runOne start $service
    fi
    ;;
  *)
    echo "Unknown operation $1"
    echo "    Please use ( start | stop | restart )"
    ;;
esac
```
