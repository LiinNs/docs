# mongodb 日常使用

~~~mongo
use admin
# 设置密码
db.createUser({
  user: 'root',
  pwd: 'xxx',
  roles:[{
    role: 'readWrite',
    db: 'admin'
  }]
})

# 验证密码
db.auth('root', 'xxx')

# 查看当前库下的用户
show users

# 删除用户
db.dropUser('testadmin')  

# 修改用户密码
db.updateUser('root', {pwd: '654321'})  

# 使用 bzeex
use bzeex

# 查看所有集合
show collections

# 查看一个 collection
db.collectionName.find()
db.getCollection("collectionName").find()

# 删除 collection 下所有数据
db.collection.remove({})
~~~

重启 mongodb as a Daemon

`mongod --fork --logpath /var/log/mongodb/mongod.log`
`mongod -f /www/server/mongodb/config.conf`

~~~conf
## content
systemLog:
  destination: file
  logAppend: true
  path: /www/server/mongodb/log/config.log
 
# Where and how to store data.
storage:
  dbPath: /www/server/mongodb/data
  directoryPerDB: true

  journal:
    enabled: true
# how the process runs
processManagement:
  fork: true
  pidFilePath: /www/server/mongodb/log/configsvr.pid
 
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0
 
#operationProfiling:
#replication:
#    replSetName: bt_main   
security:
  authorization: disabled
  javascriptEnabled: false

#sharding:
#    clusterRole: shardsvr
~~~
