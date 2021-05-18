# RocketMQ Cluster

集群配置示例 A-Master

~~~ conf
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样  例如：在a.properties 文件中写 broker-a  在b.properties 文件中写 broker-b
brokerName=broker-a
#0 表示 Master，>0 表示 Slave
brokerId=0
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#Broker 的角色，ASYNC_MASTER=异步复制Master，SYNC_MASTER=同步双写Master，SLAVE=slave节点
brokerRole=ASYNC_MASTER
#刷盘方式，ASYNC_FLUSH=异步刷盘，SYNC_FLUSH=同步刷盘 
flushDiskType=SYNC_FLUSH
#Broker 对外服务的监听端口
listenPort=10911
#nameServer地址，这里nameserver是单台，如果nameserver是多台集群的话，就用分号分割（即namesrvAddr=ip1:port1;ip2:port2;ip3:port3）
namesrvAddr=127.0.0.1:9876
#每个topic对应队列的数量，默认为4，实际应参考consumer实例的数量，值过小不利于consumer负载均衡
defaultTopicQueueNums=8
#是否允许 Broker 自动创建Topic，生产建议关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，生产建议关闭
autoCreateSubscriptionGroup=true
#设置BrokerIP
brokerIP1=127.0.0.1
#存储路径
storePathRootDir=/data/rocketmq-all-4.7.0-bin-release/data/store-a
#commitLog 存储路径
storePathCommitLog=/data/rocketmq-all-4.7.0-bin-release/data/store-a/commitlog
#消费队列存储路径存储路径
storePathConsumerQueue=/data/rocketmq-all-4.7.0-bin-release/data/store-a/consumequeue
#消息索引存储路径
storePathIndex=/data/rocketmq-all-4.7.0-bin-release/data/store-a/index
#checkpoint 文件存储路径
storeCheckpoint=/data/rocketmq-all-4.7.0-bin-release/data/store-a/checkpoint
#abort 文件存储路径
abortFile=/data/rocketmq-all-4.7.0-bin-release/data/store-a/abort
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
~~~

A-Slave

~~~ conf
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样  例如：在a.properties 文件中写 broker-a  在b.properties 文件中写 broker-b
brokerName=broker-a
#0 表示 Master，>0 表示 Slave
brokerId=1
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#Broker 的角色，ASYNC_MASTER=异步复制Master，SYNC_MASTER=同步双写Master，SLAVE=slave节点
brokerRole=SLAVE
#刷盘方式，ASYNC_FLUSH=异步刷盘，SYNC_FLUSH=同步刷盘 
flushDiskType=SYNC_FLUSH
#Broker 对外服务的监听端口
listenPort=11011
#nameServer地址，这里nameserver是单台，如果nameserver是多台集群的话，就用分号分割（即namesrvAddr=ip1:port1;ip2:port2;ip3:port3）
namesrvAddr=127.0.0.1:9876
#每个topic对应队列的数量，默认为4，实际应参考consumer实例的数量，值过小不利于consumer负载均衡
defaultTopicQueueNums=8
#是否允许 Broker 自动创建Topic，生产建议关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，生产建议关闭
autoCreateSubscriptionGroup=true
#设置BrokerIP
brokerIP1=127.0.0.1
#存储路径
storePathRootDir=/data/rocketmq-all-4.7.0-bin-release/data/store-a-s
#commitLog 存储路径
storePathCommitLog=/data/rocketmq-all-4.7.0-bin-release/data/store-a-s/commitlog
#消费队列存储路径存储路径
storePathConsumerQueue=/data/rocketmq-all-4.7.0-bin-release/data/store-a-s/consumequeue
#消息索引存储路径
storePathIndex=/data/rocketmq-all-4.7.0-bin-release/data/store-a-s/index
#checkpoint 文件存储路径
storeCheckpoint=/data/rocketmq-all-4.7.0-bin-release/data/store-a-s/checkpoint
#abort 文件存储路径
abortFile=/data/rocketmq-all-4.7.0-bin-release/data/store-a-s/abort
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
~~~

##### reference

https://github.com/apache/rocketmq/tree/master/docs/cn
https://zhuanlan.zhihu.com/p/226173170
https://cloud.tencent.com/developer/article/1412653
https://github.com/apache/rocketmq/blob/master/docs/cn/dledger/deploy_guide.md
https://github.com/apache/rocketmq/blob/master/docs/cn/best_practice.md


~~~ conf
# 所属集群名字
brokerClusterName=DefaultCluster

# broker 名字，注意此处不同的配置文件填写的不一样，如果在 broker-a.properties 使用: broker-a,
# 在 broker-b.properties 使用: broker-b
brokerName=broker-aa

# 0 表示 Master，> 0 表示 Slave
brokerId=0

# nameServer地址，分号分割
# namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
namesrvAddr=192.168.1.154:9876

# 启动IP,如果 docker 报 com.alibaba.rocketmq.remoting.exception.RemotingConnectException: connect to <192.168.0.120:10909> failed
# 解决方式1 加上一句 producer.setVipChannelEnabled(false);，解决方式2 brokerIP1 设置宿主机IP，不要使用docker 内部IP
brokerIP1=192.168.1.154

# 在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4

# 是否允许 Broker 自动创建 Topic，建议线下开启，线上关闭 ！！！这里仔细看是 false，false，false
autoCreateTopicEnable=true

# 是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true

# Broker 对外服务的监听端口
listenPort=10911

# 删除文件时间点，默认凌晨4点
deleteWhen=04

# 文件保留时间，默认48小时
fileReservedTime=120

# commitLog 每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824

# ConsumeQueue 每个文件默认存 30W 条，根据业务情况调整
mapedFileSizeConsumeQueue=300000

# destroyMapedFileIntervalForcibly=120000
# redeleteHangedFileInterval=120000
# 检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
# 存储路径
storePathRootDir=/Volumes/888/rocketmq/store
# commitLog 存储路径
storePathCommitLog=/Volumes/888/rocketmq/store/commitlog
# 消费队列存储
storePathConsumeQueue=/Volumes/888/rocketmq/store/consumequeue
# 消息索引存储路径
storePathIndex=/Volumes/888/rocketmq/store/index
# checkpoint 文件存储路径
storeCheckpoint=/Volumes/888/rocketmq/store/checkpoint
# abort 文件存储路径
abortFile=/Volumes/888/rocketmq/store/abort
# 限制的消息大小
maxMessageSize=65536

# flushCommitLogLeastPages=4
# flushConsumeQueueLeastPages=2
# flushCommitLogThoroughInterval=10000
# flushConsumeQueueThoroughInterval=60000

# Broker 的角色
# - ASYNC_MASTER 异步复制Master
# - SYNC_MASTER 同步双写Master
# - SLAVE
brokerRole=ASYNC_MASTER

# 刷盘方式
# - ASYNC_FLUSH 异步刷盘
# - SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH

# 发消息线程池数量
# sendMessageThreadPoolNums=128
# 拉消息线程池数量
# pullMessageThreadPoolNums=128
~~~