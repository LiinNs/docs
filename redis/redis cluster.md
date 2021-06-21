# Redis 集群 入门

### Redis集群特性

- 自动将数据分布到集群中的节点中
- 集群中部分节点故障或不能通信时继续操作

### Redis 集群的TCP端口

每一个集群中的节点都需要打开两个TCP端口，两个端口之间的偏移量是固定的，始终是10000。
6379 用于客户端通信、节点间keys migrations
16379 Cluster bus port,使用一种二进制协议, 用于节点间互相通信，包括失败检查、配置更新、故障转移授权(Failover Authorization)

### Redis 集群和 Docker

Redis集群不支持NAT网络环境。为了能配合Docker使用Redis集群，Docker应配置为使用宿主网络模式(host networking mode：[--net=host](https://docs.docker.com/engine/userguide/networking/dockernetworks/)

### Redis 集群的数据分片(data sharding)

Redis集群并非使用一致性哈希(consistent hashing)来进行数据分片，而是一种概念为哈希槽(hash slot)分片方式。整个集群共划分为16384个hash slot，为了计算一个Key属于的hash slot，仅需计算key的 CRC16然后相对于16384取模(we simply take the CRC16 of the key modulo 16384.)

集群内的节点负责承载整个hash slots的部分子集，例如一个三个节点的Redis集群：

- 节点A 包含 0 ~ 5500 的hash slots
- 节点B 包含 5501 ~ 11000的 hash slots
- 节点C 包含 11001~ 16383的 hash slots

### Redis 集群的主从模式（master-slave model）

A B C
A1 B1 C1

### Redis 集群的一致性保证

Redis的集群不是强一致性，有可能会发生写丢失(lose writes)。在写入一个Key时

- 客户端向B Master写入
- B Master 回复客户端 OK
- B Master 向B1 B2 B3传播Key的写入

其中 B Master是在写入成功后就回复给客户端，而不是等到所有的从节点都写入成功（会导致Redis响应过久）。如果在传播过程中B Master 宕机了，传播未完成，从节点被提升为新的主节点，就会导致写丢失，原节点恢复后会同步信息，从而写丢失被永远保存在集群中。

CAP Basically, there is a trade-off to be made between performance and consistency.

在进行网络重新划分时，写丢失也可能会发生，比如6个节点的集群和一个客户端，当 A A1 B1 C C1划为一块，Z与B划为一块，Z向B继续写入，B1后可能被选举为新的主节点，从而导致Z在B中写入的信息丢失。
但是Z的写入有一个最大的窗口时间(Maximum window),超过窗口时间后，节点会停止接受写入信息。这个窗口时间是Redis集群里的一项重要配置(node timeout)。超过这个时间，一个主节点未能与其他多数主节点相互取得联系时，会进入错误状态，停止接受写入信息。

# Redis 集群配置参数

# Redis 集群的创建和使用

最小Redis集群配置
~~~ conf
port 7000
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
~~~

~~~ bash
# 创建集群
redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 \
127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
--cluster-replicas 1

# 集群重新分片数据
redis-cli --cluster reshard 127.0.0.1:7000

# 查看节点信息
redis-cli -p 7000 cluster nodes | grep myself

# 查看集群状态
redis-cli --cluster check 127.0.0.1:7000

# 分片参数 --cluster-yes 让命令行在提示需要交互确认时总是回答yes，环境变量 REDISCLI_CLUSTER_YES 也是同等作用
redis-cli --cluster reshard <host>:<port> --cluster-from <node-id> --cluster-to <node-id> --cluster-slots <number of slots> --cluster-yes

# 关闭节点连接
redis-cli -p 7002 debug segfault
~~~

`cluster nodes`命令的输出信息

- Node ID
- ip:port
- flags: master, slave, myself, fail, ...
- if it is a slave, the Node ID of the master
- Time of the last pending PING still waiting for a reply.
- Time of the last PONG received.
- Configuration epoch for this node (see the Cluster specification).
- Status of the link to this node.
- Slots served...

### Manual failover 故障转移

主动执行故障转移，让从节点成为主节点。

首先主节点会停止与客户端的连接，同时主节点会向从节点发送当前复制动作的偏移量，待到从节点达到复制偏移量，转移便开始，主节点开始成为从节点，从节点转变为新的主节点，未被拦截的旧连接会重定向至新主节点。

### Adding new node 添加新的节点

##### 添加为主节点

`redis-cli --cluster add-node 127.0.0.1:7006 127.0.0.1:7000`

新加入的节点

- 未持有任何数据，因为还没有给他分配hash slots
- 因为还没有分配hash slots，所以在新的从节点竞选成为主节点时，当前节点也不参与

##### 添加为从节点

`redis-cli --cluster add-node 127.0.0.1:7006 127.0.0.1:7000 --cluster-slave`

`redis-cli --cluster add-node 127.0.0.1:7006 127.0.0.1:7000 --cluster-slave --cluster-master-id 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e`

`redis 127.0.0.1:7006> cluster replicate 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e`

### Removing a node 移除节点

~~~ bash
redis-cli --cluster del-node 127.0.0.1:7000 `<node-id>`
~~~

### Replicas migration 复制迁移

`CLUSTER REPLICATE <master-node-id>`

多余的从节点，避免1主1从失效
