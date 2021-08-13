# Jenkins

## shell

```bash
# /var/lib/jenkins/workspace/cloud/contract-option-api/target/
APP_NAME=${PROJECT}.jar
WORK_DIR="/www/java/"

# backup
cd ${WORK_DIR}
[ -e ${APP_NAME}_backup ] && rm -f ${APP_NAME}_backup
[ -e ${APP_NAME} ] && mv ${APP_NAME} ${APP_NAME}_backup
cp /var/lib/jenkins/workspace/cloud/${PROJECT}/target/${APP_NAME} ${WORK_DIR}
chmod 755 ${APP_NAME}

ls -al

# shutdown server
pid=`ps ax | grep -i ${APP_NAME} |grep java | grep -v grep | awk '{print $1}'`
if [ -z "$pid" ] ; then
  echo "No ${APP_NAME} running."
fi

if [ -n "$pid" ] ; then
  echo "The ${APP_NAME}(${pid}) is running..."
  kill -9 ${pid}
  echo "Shutdown ${APP_NAME}(${pid}) OK"
fi

# run server
JAVA_OPT="${JAVA_OPT} -server"

[ ${PROJECT} = "cloud" ] && JAVA_OPT="${JAVA_OPT} -Xms512m -Xmx512m"
[ ${PROJECT} = "exchange" ] && JAVA_OPT="${JAVA_OPT} -Xms4096m -Xmx4096m"
[ ${PROJECT} = "market" ] && JAVA_OPT="${JAVA_OPT} -Xms2048m -Xmx2048m"
[ ${PROJECT} = "exchange-api" ] && JAVA_OPT="${JAVA_OPT} -Xms2048m -Xmx2048m"
[ ${PROJECT} = "ucenter-api" ] && JAVA_OPT="${JAVA_OPT} -Xms2048m -Xmx2048m"
[ ${PROJECT} = "admin" ] && JAVA_OPT="${JAVA_OPT} -Xms2048m -Xmx2048m"
[ ${PROJECT} = "contract-swap-api" ] && JAVA_OPT="${JAVA_OPT} -Xms3072m -Xmx3072m"
[ ${PROJECT} = "contract-option-api" ] && JAVA_OPT="${JAVA_OPT} -Xms2048m -Xmx2048m"


BUILD_ID=dontKillM nohup java ${JAVA_OPT} -jar ${APP_NAME} >/dev/null 2>&1 &

jps -vl
```

## reset password

[0](https://www.serverlab.ca/tutorials/linux/administration-linux/how-to-reset-jenkins-admin-users-password/)
