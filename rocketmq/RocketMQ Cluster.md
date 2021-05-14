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