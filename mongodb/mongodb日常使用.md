# mongodb 日常使用

~~~mongo
# 使用bzeex
use bzeex

# 查看所有集合
show collections

# 查看一个collection
db.collectionName.find()
db.getCollection("collectionName").find()

# 删除collection下所有数据
db.collection.remove({})
~~~
