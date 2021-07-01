# shell脚本样例学习

`\` 换行符

文件表达式：

* -e filename 如果 filename存在，则为真
* -d filename 如果 filename为目录，则为真
* -f filename 如果 filename为常规文件，则为真
* -L filename 如果 filename为符号链接，则为真
* -r filename 如果 filename可读，则为真
* -w filename 如果 filename可写，则为真
* -x filename 如果 filename可执行，则为真
* -s filename 如果文件长度不为0，则为真
* -h filename 如果文件是软链接，则为真
* filename1 -nt filename2 如果 filename1比 filename2新，则为真。
* filename1 -ot filename2 如果 filename1比 filename2旧，则为真。

数量变量表达式

* if  [ $a = $b ]                 如果string1等于string2，则为真，字符串允许使用赋值号做等号
* if  [ $string1 !=  $string2 ]   如果string1不等于string2，则为真       
* if  [ -n $string  ]             如果string 非空(非0），返回0(true)  
* if  [ -z $string  ]             如果string 为空，则为真
* if  [ $sting ]                  如果string 非空，返回0 (和-n类似)

比较运算符：

* -eq 等于
* -ne 不等于
* -gt 大于
* -ge 大于等于
* -lt 小于
* -le 小于等于

逻辑关系：

* 逻辑非 !
* 逻辑与 -a
* 逻辑或 -o

\`dirname $0\`返回当前路径的"."

~~~bash
#!/bin/bash
echo "\$*=" $*
echo "\"\$*\"=" "$*"
echo "\$@=" $@
echo "\"\$@\"=" "$@"
echo "print each param from \$*"
for var in $*
do
    echo "$var"
done
echo "print each param from \$@"
for var in $@
do
    echo "$var"
done
echo "print each param from \"\$*\""
for var in "$*"
do
    echo "$var"
done
echo "print each param from \"\$@\""
for var in "$@"
do
    echo "$var"
done
~~~

~~~bash
#!/bnin/sh
# 进入上层目录
TESTDIR=$(dirname $0)/..
cd $TESTDIR
# ${TESTDIR}
echo `pwd`
~~~

shell特殊变量

* $0：当前脚本的文件名
* $n：传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。
* $#：传递给脚本或函数的参数个数。
* $*：传递给脚本或函数的所有参数。
* $@：传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同
* $?：上个命令的退出状态，或函数的返回值。
* $$：当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。

`read` 交互式从stdin读取输入
`readonly` 只读变量
`unset` 删除变量

~~~bash
#!/bin/sh

# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

#===========================================================================================
# Java Environment Setting
#===========================================================================================
error_exit ()
{
    echo "ERROR: $1 !!"
    exit 1
}

[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=$HOME/jdk/java
[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/usr/java
[ ! -e "$JAVA_HOME/bin/java" ] && error_exit "Please set the JAVA_HOME variable in your environment, We need java(x64)!"

export JAVA_HOME
export JAVA="$JAVA_HOME/bin/java"
export BASE_DIR=$(dirname $0)/..
export CLASSPATH=.:${BASE_DIR}/conf:${CLASSPATH}

#===========================================================================================
# JVM Configuration
#===========================================================================================
# The RAMDisk initializing size in MB on Darwin OS for gc-log
DIR_SIZE_IN_MB=600

choose_gc_log_directory()
{
    case "`uname`" in
        Darwin)
            if [ ! -d "/Volumes/RAMDisk" ]; then
                # create ram disk on Darwin systems as gc-log directory
                DEV=`hdiutil attach -nomount ram://$((2 * 1024 * DIR_SIZE_IN_MB))` > /dev/null
                diskutil eraseVolume HFS+ RAMDisk ${DEV} > /dev/null
                echo "Create RAMDisk /Volumes/RAMDisk for gc logging on Darwin OS."
            fi
            GC_LOG_DIR="/Volumes/RAMDisk"
        ;;
        *)
            # check if /dev/shm exists on other systems
            if [ -d "/dev/shm" ]; then
                GC_LOG_DIR="/dev/shm"
            else
                GC_LOG_DIR=${BASE_DIR}
            fi
        ;;
    esac
}

choose_gc_log_directory

JAVA_OPT="${JAVA_OPT} -server -Xms8g -Xmx8g -Xmn4g"
JAVA_OPT="${JAVA_OPT} -XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:G1ReservePercent=25 -XX:InitiatingHeapOccupancyPercent=30 -XX:SoftRefLRUPolicyMSPerMB=0"
JAVA_OPT="${JAVA_OPT} -verbose:gc -Xloggc:${GC_LOG_DIR}/rmq_broker_gc_%p_%t.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCApplicationStoppedTime -XX:+PrintAdaptiveSizePolicy"
JAVA_OPT="${JAVA_OPT} -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=30m"
JAVA_OPT="${JAVA_OPT} -XX:-OmitStackTraceInFastThrow"
JAVA_OPT="${JAVA_OPT} -XX:+AlwaysPreTouch"
JAVA_OPT="${JAVA_OPT} -XX:MaxDirectMemorySize=15g"
JAVA_OPT="${JAVA_OPT} -XX:-UseLargePages -XX:-UseBiasedLocking"
JAVA_OPT="${JAVA_OPT} -Djava.ext.dirs=${JAVA_HOME}/jre/lib/ext:${BASE_DIR}/lib:${JAVA_HOME}/lib/ext"
#JAVA_OPT="${JAVA_OPT} -Xdebug -Xrunjdwp:transport=dt_socket,address=9555,server=y,suspend=n"
JAVA_OPT="${JAVA_OPT} ${JAVA_OPT_EXT}"
JAVA_OPT="${JAVA_OPT} -cp ${CLASSPATH}"

numactl --interleave=all pwd > /dev/null 2>&1
if [ $? -eq 0 ]
then
        if [ -z "$RMQ_NUMA_NODE" ] ; then
                numactl --interleave=all $JAVA ${JAVA_OPT} $@
        else
                numactl --cpunodebind=$RMQ_NUMA_NODE --membind=$RMQ_NUMA_NODE $JAVA ${JAVA_OPT} $@
        fi
else
        $JAVA ${JAVA_OPT} $@
fi
~~~

~~~bash
#!/bin/sh

# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

case $1 in
    broker)

    pid=`ps ax | grep -i 'org.apache.rocketmq.broker.BrokerStartup' |grep java | grep -v grep | awk '{print $1}'`
    if [ -z "$pid" ] ; then
            echo "No mqbroker running."
            exit -1;
    fi

    echo "The mqbroker(${pid}) is running..."

    kill ${pid}

    echo "Send shutdown request to mqbroker(${pid}) OK"
    ;;
    namesrv)

    pid=`ps ax | grep -i 'org.apache.rocketmq.namesrv.NamesrvStartup' |grep java | grep -v grep | awk '{print $1}'`
    if [ -z "$pid" ] ; then
            echo "No mqnamesrv running."
            exit -1;
    fi

    echo "The mqnamesrv(${pid}) is running..."

    kill ${pid}

    echo "Send shutdown request to mqnamesrv(${pid}) OK"
    ;;
    *)
    echo "Useage: mqshutdown broker | namesrv"
esac

~~~

## references

[linux 下shell中if的“-e，-d，-f”是什么意思](https://blog.csdn.net/superbfly/article/details/49274889)
[numactl manual](https://man7.org/linux/man-pages/man8/numactl.8.html)
[鲲鹏通用](https://support.huaweicloud.com/tuningtip-kunpenggrf/kunpengtuning_12_0009.html)
[Shell简介 - shell脚本变量](http://c.biancheng.net/cpp/view/2739.html)
[脚本编写经验](https://mp.weixin.qq.com/s/22awzL9yNoEa8kSyUt0fog)
