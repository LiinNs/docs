# Redis 脚本 EVEL

`EVAL script numkeys key [key ...] arg [arg ...]`

示例

~~~bash
eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second

eval "return redis.call('keys', 'CMS*')" 0
~~~

redis.call 向执行命令的客户端抛出error

redis.pcall 会捕获error,并返回代表错误的Lua表

EVAL 脚本使用的 redis key 应该全部当成参数，通过 KEYS数组 传入

以下Redis脚本效果相同
~~~ redis
eval 'return redis.status_reply("Good Job!")' 0
eval 'return {ok="Good Job!"}' 0

eval 'return redis.error_rreply("My Error!")' 0
eval 'return {err="My Error!"}' 0
~~~