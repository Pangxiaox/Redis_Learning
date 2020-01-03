# Redis学习笔记

### 1. 什么是Redis

Remote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。

Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

它通常被称为数据结构服务器，因为值（value）可以是字符串(String), 哈希(Hash), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

Redis支持数据的备份，即master-slave模式的数据备份。（主从同步）



### 2. Redis安装与配置

打开cmd窗口，使用cd命令移动到redis所在的文件夹，然后输入命令：

```shell
redis-server.exe redis.windows.conf
```

此时再打开另一个cmd窗口，使用cd命令移动到redis所在的文件夹，然后输入命令：

```shell
redis-cli.exe -h 127.0.0.1 -p 6379
```

测试：设置键值对和取出键值对

```shell
127.0.0.1:6379> set myKey a
OK
127.0.0.1:6379> get myKey
"a"
```

查看配置：

```shell
redis 127.0.0.1:6379> CONFIG GET CONFIG_SETTING_NAME
```

```shell
127.0.0.1:6379> CONFIG GET loglevel
1) "loglevel"
2) "notice"
```

```shell
127.0.0.1:6379> CONFIG GET *
//省略输出结果//
```

编辑配置：

```shell
redis 127.0.0.1:6379> CONFIG SET CONFIG_SETTING_NAME NEW_CONFIG_VALUE
```

```shell
127.0.0.1:6379> CONFIG SET loglevel "notice"
OK
127.0.0.1:6379> CONFIG GET loglevel
1) "loglevel"
2) "notice"
```



### 3. Redis数据类型

##### 3.1 String（字符串）

string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。

```shell
127.0.0.1:6379> SET test "TEST"
OK
127.0.0.1:6379> GET test
"TEST"
```

key为test，value为TEST字符串

##### 3.2  Hash（哈希）

Redis hash 是一个键值(key=>value)对集合。

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

```shell
127.0.0.1:6379> DEL test
(integer) 1
127.0.0.1:6379> HMSET test key1 "a" key2 "b"
OK
127.0.0.1:6379> HGET test key1
"a"
127.0.0.1:6379> HGET test key2
"b"
```

##### 3.3 List（列表）

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

```shell
127.0.0.1:6379> DEL test
(integer) 1
127.0.0.1:6379> lpush test 1
(integer) 1
127.0.0.1:6379> lpush test 2
(integer) 2
127.0.0.1:6379> lpush test 3
(integer) 3
127.0.0.1:6379> lrange test 0 5
1) "3"
2) "2"
3) "1"
```

##### 3.4 Set（集合）

Redis 的 Set 是 string 类型的无序集合。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

sadd命令：添加一个 string 元素到 key 对应的 set 集合中，成功返回 1，如果元素已经在集合中返回 0。

```shell
127.0.0.1:6379> DEL test
(integer) 1
127.0.0.1:6379> sadd test 2
(integer) 1
127.0.0.1:6379> sadd test 4
(integer) 1
127.0.0.1:6379> sadd test 6
(integer) 1
127.0.0.1:6379> sadd test 4
(integer) 0
127.0.0.1:6379> smembers test
1) "2"
2) "4"
3) "6"
```

##### 3.5 zset(sorted set 有序集合)

Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。

zset的成员是唯一的,但分数(score)却可以重复。

```shell
127.0.0.1:6379> DEL test
(integer) 1
127.0.0.1:6379> zadd test 0 redis
(integer) 1
127.0.0.1:6379> zadd test 0 rabbitmq
(integer) 1
127.0.0.1:6379> zadd test 0 mongodb
(integer) 1
127.0.0.1:6379> zadd test 0 redis
(integer) 0
127.0.0.1:6379> ZRANGEBYSCORE test 0 1000
1) "mongodb"
2) "rabbitmq"
3) "redis"
```



### 4. Redis命令

redis客户端的基本语法：

```shell
$ redis-cli
```

在远程redis服务上执行命令：

```shell
$ redis-cli -h host -p port -a password
```

```shell
D:\Redis>redis-cli.exe -h 127.0.0.1 -p 6379
127.0.0.1:6379> PING
PONG
```



### 5. Redis键

Redis 键命令用于管理 redis 的键。

基本语法：

```shell
redis 127.0.0.1:6379> COMMAND KEY_NAME
```

部分关于键的命令：

| 命令               | 描述                              |
| ------------------ | --------------------------------- |
| EXISTS key         | 检查给定key是否存在               |
| DEL key            | 用于在key存在时删除key            |
| EXPIRE key seconds | 为给定key设置过期时间，单位是秒   |
| RANDOMKEY          | 从当前数据库中随机返回一个key     |
| RENAME key newkey  | 修改key的名称为newkey             |
| TYPE key           | 返回key所存储的值的类型           |
| DUMP key           | 序列化给定key，并返回被序列化的值 |



### 6. Redis字符串

Redis 字符串数据类型的相关命令用于管理 redis 字符串值。

基本语法：

```shell
redis 127.0.0.1:6379> COMMAND KEY_NAME
```

部分关于字符串的命令：

| 命令                   | 描述                                                       |
| ---------------------- | ---------------------------------------------------------- |
| SET key value          | 设置指定key的值                                            |
| GET key                | 获取指定key的值                                            |
| GETSET key value       | 将给定 key 的值设为 value ，并返回 key 的旧值(old value)   |
| MGET key1 [key2...]    | 获取所有（一个或多个）给定key的值                          |
| GETRANGE key start end | 返回key中字符串值的子字符（索引从start开始到end，包含end） |



### 7. Redis哈希

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

部分关于哈希的命令：

| 命令                                       | 描述                                              |
| ------------------------------------------ | ------------------------------------------------- |
| HMSET key field1 value1 [field2 value2...] | 同时将多个field-value（域-值）对设置到哈希表key中 |
| HMGET key field1 [field2...]               | 获取所有给定字段的值                              |
| HDEL key field1 [field2...]                | 删除一个或多个哈希表字段                          |
| HEXISTS key field                          | 查看哈希表key中，指定字段是否存在                 |
| HGETALL key                                | 获取在哈希表中指定key的所有字段和值               |
| HGET key field                             | 获取存储在哈希表中指定字段的值                    |
| HKEYS key                                  | 获取所有哈希表中的字段                            |
| HVALS key                                  | 获取哈希表中所有的值                              |
| HLEN key                                   | 获取哈希表中字段的数量                            |



### 8. Redis列表

Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

部分关于列表的命令：

| 命令                         | 描述                                     |
| ---------------------------- | ---------------------------------------- |
| LINDEX key index             | 通过索引获取列表中的元素                 |
| LLEN key                     | 获取列表长度                             |
| LPOP key                     | 移除并获取列表的第一个元素               |
| LPUSH key value1 [value2...] | 将一个或多个值插入到列表头部             |
| LPUSHX key value             | 将一个值插入到已存在的列表头部           |
| LRANGE key start end         | 获取列表指定范围内的元素                 |
| RPOP key                     | 移除列表的最后一个元素，返回被移除的元素 |
| RPUSH key value1 [value2...] | 将一个或多个值插入到列表尾部             |
| RPUSHX key value             | 将一个值插入到列表尾部                   |



### 9. Redis集合

Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。 Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

部分关于集合的命令：

| 命令                                   | 描述                                        |
| -------------------------------------- | ------------------------------------------- |
| SADD key member1 [member2...]          | 向集合中添加一个或多个成员                  |
| SCARD key                              | 获取集合的成员数                            |
| SDIFF key1 [key2...]                   | 返回给定所有集合的差集                      |
| SINTER key1 [key2...]                  | 返回给定所有集合的交集                      |
| SMEMBERS key                           | 返回集合中的所有成员                        |
| SUNION key1 [key2...]                  | 返回所有给定集合的并集                      |
| SPOP key                               | 移除并返回集合中的一个随机元素              |
| SREM key member1 [member2...]          | 移除集合中的一个或多个成员                  |
| SINTERSTORE destination key1 [key2...] | 返回给定所有集合的交集并存储在destination中 |



### 10. Redis有序集合

Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。

有序集合的成员是唯一的,但分数(score)却可以重复。

示例：

```shell
127.0.0.1:6379> zadd zset 2 a 0 z 1 b
(integer) 3
127.0.0.1:6379> ZRANGE zset 0 3
1) "z"
2) "b"
3) "a"
```

部分关于有序集合的命令：

| 命令                                        | 描述                                                     |
| ------------------------------------------- | -------------------------------------------------------- |
| ZADD key score1 member1 [score2 member2...] | 向有序集合中添加一个或多个成员，或者更新已存在成员的分数 |
| ZCARD key                                   | 获取有序集合的成员数                                     |
| ZCOUNT key min max                          | 计算在有序集合中指定区间分数的成员数                     |
| ZRANK key member                            | 返回有序集合中指定成员的索引                             |
| ZREM key member1 [member2...]               | 移除有序集合中的一个或多个成员                           |
| ZRANGE key start stop [WITHSCORES]          | 通过索引区间返回有序集合指定区间内的成员                 |
| ZSCORE key member                           | 返回有序集合中指定成员的分数值                           |



### 11. Redis HyperLogLog

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

🔺什么是基数？

比如数据集{1，3，5，6，6，8}，那么这个数据集的基数集就是{1，3，5，6，8}，基数（不重复元素个数）为5。基数估计就是在误差可接受的范围内，快速计算基数。

示例：

```shell
127.0.0.1:6379> PFADD pf 1
(integer) 1
127.0.0.1:6379> PFADD pf 2
(integer) 1
127.0.0.1:6379> PFADD pf 3
(integer) 1
127.0.0.1:6379> PFADD pf 2
(integer) 0
127.0.0.1:6379> PFADD pf 12
(integer) 1
127.0.0.1:6379> PFCOUNT pf
(integer) 4
```



### 12. Redis发布-订阅

Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

Redis 客户端可以订阅任意数量的频道。

![Image text](https://github.com/Pangxiaox/Redis_Learning/blob/master/img/pubsub1.png)

![Image text](https://github.com/Pangxiaox/Redis_Learning/blob/master/img/pubsub2.png)

示例：

创建了订阅频道名为redisChat

```shell
127.0.0.1:6379> SUBSCRIBE redisChat
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "redisChat"
3) (integer) 1
```

新开启一个redis客户端，然后在同一频道redisChat发布两次消息，订阅者能收到消息。

发布者客户端：

```shell
D:\Redis>redis-cli.exe -h 127.0.0.1 -p 6379
127.0.0.1:6379> PUBLISH redisChat "111"
(integer) 1
127.0.0.1:6379> PUBLISH redisChat "222"
(integer) 1
```

订阅者客户端：

```shell
127.0.0.1:6379> SUBSCRIBE redisChat
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "redisChat"
3) (integer) 1
1) "message"
2) "redisChat"
3) "111"
1) "message"
2) "redisChat"
3) "222"
```

此外，退订给定的频道可以采用以下命令：

```
127.0.0.1:6379> UNSUBSCRIBE [channel]
```



### 13. Redis事务

Redis事务可以一次执行多个命令，并且带有以下三个重要保证：

- 批量操作在发送EXEC命令前被放入队列缓存
- 收到EXEC命令后进入事务执行，事务中任意命令执行失败，其余命令依然被执行
- 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中

一个事务从开始到执行会经历以下三个阶段：

- 开始事务
- 命令入队
- 执行事务

```shell
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> SET name "Ann"
QUEUED
127.0.0.1:6379> GET name
QUEUED
127.0.0.1:6379> SADD money 150 200 250
QUEUED
127.0.0.1:6379> SMEMBERS money
QUEUED
127.0.0.1:6379> EXEC
1) OK
2) "Ann"
3) (integer) 3
4) 1) "150"
   2) "200"
   3) "250"
```

🔺单个Redis命令的执行是原子性的，但Redis没有在事务上增加任何维持原子性的机制，所以Redis事务的执行并不是原子性的。

Redis事务命令：

| 命令                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| DISCARD              | 取消事务，放弃执行事务块内的所有命令                         |
| EXEC                 | 执行所有事务块内的命令                                       |
| MULTI                | 标记一个事务块的开始                                         |
| UNWATCH              | 取消WATCH命令对所有key的监视                                 |
| WATCH key1 [key2...] | 监视一个（或多个）key，如果在事务执行之前这个（或这些）key被其他命令所改动，那么事务将被打断 |



### 14. Redis脚本

Redis脚本使用Lua解释器来执行脚本，执行脚本的常用命令为EVAL

基本语法：

```shell
redis 127.0.0.1:6379> EVAL script numkeys key [key ...] arg [arg ...]
```

示例：

```shell
127.0.0.1:6379> EVAL "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 k1 k2 1 2
1) "k1"
2) "k2"
3) "1"
4) "2"
```

Redis脚本命令：

| 命令                                               | 描述                                                 |
| -------------------------------------------------- | ---------------------------------------------------- |
| EVAL script numkeys key1 [key2...] arg1 [arg2...]  | 执行Lua脚本                                          |
| EVALSHA sha1 numkeys key1 [key2...] arg1 [arg2...] | 执行Lua脚本                                          |
| SCRIPT EXISTS script1 [script2...]                 | 查看指定的脚本是否已经被保存在缓存当中               |
| SCRIPT FLUSH                                       | 从脚本缓存中移除所有脚本                             |
| SCRIPT KILL                                        | 杀死当前正在运行的Lua脚本                            |
| SCRIPT LOAD script                                 | 将脚本script添加到脚本缓存中，但并不立即执行这个脚本 |



### 15. Redis连接

Redis连接命令主要是用于连接redis服务

Redis连接命令：

| 命令          | 描述                               |
| ------------- | ---------------------------------- |
| AUTH password | 验证密码是否正确                   |
| ECHO message  | 打印字符串                         |
| PING          | 查看服务是否运行（正常时回应PONG） |
| QUIT          | 关闭当前连接                       |
| SELECT index  | 切换到指定的数据库                 |



### 16. Redis服务器

Redis服务器命令主要是用于管理redis服务

Redis服务器命令：

| 命令           | 描述                                |
| -------------- | ----------------------------------- |
| TIME           | 返回当前服务器时间                  |
| DBSIZE         | 返回当前数据库的key的数量           |
| INFO [section] | 获取Redis服务器的各种信息和统计数值 |
| FLUSHALL       | 删除所有数据库的所有key             |
| FLUSHDB        | 删除当前数据库的所有key             |



### 17. Redis数据备份与恢复

##### 17.1 数据备份

基本语法：

```shell
redis 127.0.0.1:6379> SAVE 
```

```shell
127.0.0.1:6379> SAVE
OK
```

该命令将在redis安装目录中创建dump.rdb文件

此外，创建redis备份文件也可以使用命令BGSAVE，该命令在后台执行

```shell
127.0.0.1:6379> BGSAVE
Background saving started
```

##### 17.2 数据恢复

如果需要恢复数据，只需将备份数据(dump.rdb)移动到redis安装目录并启动服务即可



### 18. Redis安全

我们可以通过 redis 的配置文件设置密码参数，这样客户端连接到 redis 服务就需要密码验证，这样可以让你的 redis 服务更安全。

默认情况下requirepass参数为空，我们设置密码为test

```shell
127.0.0.1:6379> CONFIG set requirepass "test"
```

设置密码后，客户端连接redis服务就需要密码验证，否则无法执行命令

```shell
127.0.0.1:6379> RANDOMKEY
(error) NOAUTH Authentication required.
127.0.0.1:6379> AUTH "t"
(error) ERR invalid password
127.0.0.1:6379> AUTH "test"
OK
127.0.0.1:6379> CONFIG get requirepass
1) "requirepass"
2) "test"
```



### 19. Redis性能测试

Redis性能测试是通过同时执行多个命令实现的

在redis目录下执行该命令：

```shell
redis-benchmark [option] [option value]
```

以下示例同时执行10000个请求检测性能：

```shell
D:\Redis>redis-benchmark -n 10000 -q
PING_INLINE: 21276.60 requests per second
PING_BULK: 21459.23 requests per second
SET: 20790.02 requests per second
GET: 20618.56 requests per second
INCR: 21321.96 requests per second
LPUSH: 20964.36 requests per second
LPOP: 21321.96 requests per second
SADD: 21645.02 requests per second
SPOP: 21413.28 requests per second
LPUSH (needed to benchmark LRANGE): 21367.52 requests per second
LRANGE_100 (first 100 elements): 21321.96 requests per second
LRANGE_300 (first 300 elements): 21186.44 requests per second
LRANGE_500 (first 450 elements): 20876.83 requests per second
LRANGE_600 (first 600 elements): 21739.13 requests per second
MSET (10 keys): 20283.98 requests per second
```

以下示例使用多个参数来测试性能：

```shell
D:\Redis>redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 10000 -q
SET: 20833.33 requests per second
LPUSH: 20920.50 requests per second
```

主机为127.0.0.1，端口号为6379，执行的命令为set和lpush，请求数为10000，通过-q参数让结果只显示每秒执行的请求数



### 20. Redis客户端连接

Redis 通过监听一个 TCP 端口或者 Unix socket 的方式来接收来自客户端的连接，当一个连接建立后，Redis 内部会进行以下一些操作：

- 首先，客户端 socket 会被设置为非阻塞模式，因为 Redis 在网络事件处理上采用的是非阻塞多路复用模型。
- 然后为这个 socket 设置 TCP_NODELAY 属性，禁用 Nagle 算法
- 然后创建一个可读的文件事件用于监听这个客户端 socket 的数据发送

##### 最大连接数

maxclients默认为10000

```shell
127.0.0.1:6379> config get maxclients
1) "maxclients"
2) "10000"
```

以下我们在服务启动时设置最大连接数为100000

```shell
redis-server --maxclients 100000
```



### 21. Redis管道技术

Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务。这意味着通常情况下一个请求会遵循以下步骤：

- 客户端向服务端发送一个查询请求，并监听Socket返回，通常是以阻塞模式，等待服务端响应
- 服务端处理命令，并将结果返回给客户端

🔺Redis 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应。

🔺管道技术最显著的优势是提高了redis服务性能



### 22. Redis分区

分区是分割数据到多个Redis实例的处理过程，因此每个实例只保存key的一个子集。

##### 22.1 分区优势

- 通过利用多台计算机内存的和值，允许我们构造更大的数据库。
- 通过多核和多台计算机，允许我们扩展计算能力；通过多台计算机和网络适配器，允许我们扩展网络带宽。

##### 22.2 分区不足

- 涉及多个key的操作通常是不被支持的。举例来说，当两个set映射到不同的redis实例上时，你就不能对这两个set执行交集操作。
- 涉及多个key的redis事务不能使用。

##### 22.3 分区类型

有两种类型分区，范围分区和哈希分区

