# Redis 脚本 EVEL

https://redis.io/commands/eval

`EVAL script numkeys key [key ...] arg [arg ...]`

示例

~~~ lua
eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second

eval "return redis.call('keys', 'CMS*')" 0
~~~

redis.call 向执行命令的客户端抛出error

redis.pcall 会捕获error,并返回代表错误的Lua表

EVAL 脚本使用的 redis key 应该全部当成参数，通过 KEYS数组 传入

以下Redis脚本效果相同
~~~ lua
eval 'return redis.status_reply("Good Job!")' 0
eval 'return {ok="Good Job!"}' 0

eval 'return redis.error_rreply("My Error!")' 0
eval 'return {err="My Error!"}' 0
~~~

### 在低内存状况下运行 Lua 脚本

redis运行超过`maxmemmory`时,执行Lua脚本可能会导致自身被放弃执行。

在以下状态下可以继续执行

- 脚本的首条写命令不消耗额外的内存，如: DEL、LREM、SREM等，在这种状况下，Lua继续执行会使Redis使用超过`maxmemory`配置的上限
- 脚本执行时，Redis当前使用的memory稍微低于`maxmemory`，第一行命令所需的memory是允许范围内的，但是随着时间的推移，所使用的memory允许超过`maxmemory`

在这种情况下可以配置redis的最大内存使用策略`maxmemory-policy`，不去驱逐脚本执行。

在EVAL脚本被执行过后，可以使用EVALSHA来优化带宽的使用，如果Redis 返回 NOSCRIPT的错误，可以继续使用EVAL脚本重试。EVALSHA 使用的参数是使用SHA1加密的EVAL脚本字符串。

在一个实例需要重新初始化给其他顾客或应用使用时，执行 `SCRIPT FLUSH [ASYNC|SYNC]`来清理脚本缓存。

`SCRIPT LOAD script` 在Redis SCRIPT缓存中注册当前脚本，不必执行脚本，以便在pipeline 和 MULTI/EXEC中重复使用

~~~ lua
eval 'return 10' 0
-- 10

SCRIPT LOAD 'return 10'
-- 080c414e64bca1184bc4f6220a19c4d495ac896d

evalsha 080c414e64bca1184bc4f6220a19c4d495ac896d 0
-- 10
~~~

### 脚本复制模式

### 脚本效果复制模式

### 选择复制

~~~ lua
redis.set_repl(redis.REPL_ALL) -- Replicate to AOF and replicas.
redis.set_repl(redis.REPL_AOF) -- Replicate only to AOF.
redis.set_repl(redis.REPL_REPLICA) -- Replicate only to replicas (Redis >= 5)
redis.set_repl(redis.REPL_SLAVE) -- Used for backward compatibility, the same as REPL_REPLICA.
redis.set_repl(redis.REPL_NONE) -- Don't replicate at all.

redis.replicate_commands() -- Enable effects replication.
redis.call('set','A','1')
redis.set_repl(redis.REPL_NONE)
redis.call('set','B','2')
redis.set_repl(redis.REPL_ALL)
redis.call('set','C','3')

select 10

flushdb

hello

info 

client list

redis.setresp(3)

config get lua-time-limit
config get *
~~~

以上 只有A、C 相关的指令会被复制到Slave和AOF

### 全局变量保护 Global variables protection

脚本不能创建全局变量

### Available libraries 可用库

+ base lib.
+ table lib.
+ string lib.
+ math lib.
+ struct lib.
+ cjson lib.
+ cmsgpack lib.
+ bitop lib.
+ redis.sha1hex function.
+ redis.breakpoint and redis.debug function in the context of the [Redis Lua debugger.](https://redis.io/topics/ldb)

### 执行超时

5秒 SCRIPT KILL SHUTDOWN NOSAVE