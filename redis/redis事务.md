# Transactions

MULTI EXEC DISCARD WATCH是Redis事务的基石。

- 在事务内的所有命令依次执行，执行过程中不会突然去处理其他请求
- 要么全部执行，要么压根不执行。由EXEC指令触发事务中命令的执行。AOF Append only file，redis-check-aof
- CAS 乐观锁中，会使用一种类似check-and-set的方式来操作

事务以`MULTI`开始记录，Redis不会立即执行在MULTI指令之后的操作，而是先依次记录下来，待到`EXEC`触发事务执行。否则输入`DISCARD`清空事务的指令队列，并退出事务。

~~~ Redis
> MULTI
OK
> INCR foo
QUEUED
> INCR bar
QUEUED
> EXEC
1) (integer) 1
2) (integer) 1
~~~

`EXEC`触发执行后，所有的返回对应事务队列中指令的执行结果。由`MULTI`开始事务后，之后的指令都将返回`QUEUED`，然后排队到事务队列中，直到执行EXEC。

## 事务中发生错误 Errors inside a transaction

在Redis事务中有可能会发生两种指令错误状态

- 指令进入事务队列失败，会在执行`EXEC`前发生。语法错误 参数错误 指令名称不正确等；
- 在执行`EXEC`后，事务中的指令执行出错。比如错误使用了与Key数据结构不同的指令读取信息。

通常第一种错误类型较为容易察觉，如果一条指令进入事务队列成功，会返回QUEUED，而排队失败时，会返回一条错误信息。在指令排队失败时，多数的Redis 客户端会退出事务状态。
在版本2.6.5以上的Redis 服务上，Redis 服务会记录进入事务队列失败的信息，并在EXEC执行事务时拒绝执行，并返回对应的错误信息，自动的退出事务状态。

第二种错误状况下，事务中的其他的指令会继续执行。

~~~ Redis
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
MULTI
+OK
SET a abc
+QUEUED
LPOP a
+QUEUED
EXEC
*2
+OK
-ERR Operation against a key holding the wrong kind of value
~~~

## 为什么Redis不支持回滚 Why Redis does not surpport roll backs?

对于Redis这种，事务中命令执行错误，不会滚而继续执行的行为，有关系型数据库背景的用户可能会觉得奇怪。但是这种处理方式也有好处

- Redis 指令只会在句法错误、错误使用了与Key数据结构不同的指令。这意味着这些只是程序错误，在开发时是很有可能被发现的，不会被遗留到线上；
- 正因如此，Redis才更坚定、运行更快，因为压根不用处理回滚的状况。

对于Redis这个特性的争论，通常在于，对于这些错误哪怕Redis提供回滚机制，也帮不到程序员，程序内可能自增了一个错误的Key，或者自增的步长是2，而不是1。

## 清空指令队列 Discarding the command queue

`DISCARD`,执行了这个指令后，事务队列中的指令会被清理，连接退出事务状态。

~~~ redis
> SET foo 1
OK
> MULTI
OK
> INCR foo
QUEUED
> DISCARD
OK
> GET foo
"1"
~~~

## 使用CAS(check-and-set)的乐观锁

`WATCH`为Redis的事务提供了check-and-set的方式，可以检测到被监视的keys的变化。如果被监视的key，在事务执行EXEC之前被修改了，那么整个事务将被终止，EXEC指令会产生`NULL reply`来告知事务执行失败。
假设有如下场景，我们想对一个值自增1(设想Redis还没提供INCR)。我们能像下面这样干：

~~~redis
val = GET mykey
val = val + 1
SET mykey $val
~~~

当我们只有一个客户端在执行操作时，这个指令是可靠的。但如果多个客户端同时执行操作，就会产生竞态条件race condition。比如客户端A、B同时读取到值10，然后都对其+1，最终将11设置回去。这种状况下最终得到的值就是11而不是我们所期待的12。

而`WATCH`指令可以很好地应对这种问题。

~~~mysql
WATCH mykey
val = GET mykey
val = val + 1
MULTI
SET mykey $val
EXEC
~~~

## 对WATCH的解释

`WATCH`会使`EXEC`有条件的去执行，如果被监视的keys在执行`EXEC`前未被修改过，除了事务内对被监视的keys的修改，则事务可以成功去执行，否则压根不会进入事务。
另外如果被监视的keys被Redis自动的过期了，则`EXEC`仍继续执行。
可以多次执行`WATCH`监视keys，从`WATCH`执行开始，到`EXEC`，keys会一直处于监视状态下。你还可以同时在一个`WATCH`指令下监视多个Key。
`EXEC`执行后，keys就不再被监视。连接被关闭后，keys也全不再被监视。

`UNWATCH`指令也可以不再监视所有的keys。

## Redis scripting and transactions

redis scripting始终是在事务中执行得。

## references

[transactions](https://redis.io/topics/transactions)
