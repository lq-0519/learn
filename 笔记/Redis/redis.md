# redis-分片

分片: 把你的数据拆分到多个redis实例中, 这样每个redis实例就包含所有key的一个子集

## 分片的缺点

不能对映射到不通分片的key进行集合运算

# 单线程处理模式

server为单线程处理, 在单线程处理用户请求的时候, 还会插入一些定时任务

1. 过期key删除
2. 链接超时检查
3. AOF文件重写
4. 扩容存放数据的dic容量

这些定时任务大概100ms会执行一次

# 热key的危害

1. 会导致请求过于集中, 使某一个shard压力过大, 不能发挥集群的优势
2. shard压力过大不能通过扩容解决, 只能增加一个shard的副本数量

# LRU算法

Least Recently Used 

这种算法认为, 最近使用过的数据都是有用的, 很久没用过的数据都是没用的, 会被优先删除.

几种淘汰策略

1. volatile-lru：只对设置了过期时间的key进行LRU（默认值）；
2. allkeys-lru ： 删除lru算法的key；
3. volatile-random：随机删除即将过期key；
4. allkeys-random：随机删除；
5. noeviction ： 永不过期，返回错误

# redis命令

## sMembers

返回set集合中所有成员

## sRem

删除集合中一个或多个元素, 不存在的元素会被忽略

redis Srem 命令基本语法如下：

```
redis 127.0.0.1:6379> SREM KEY MEMBER1..MEMBERN
```

## sCard

Redis 的 sCard命令用于返回 集合 KEY 的元素的数量。

## TTL

以秒为单位返回key剩余的过期时间

```redis
TTL KEY_NAME
```

## setEx

为指定的key设置值, 并设置过期时间, 这是一个原子操作, 不管key是否存在都会设置

## incrBy

Redis Incrby 命令将 key 中储存的数字加上指定的增量值。

如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCRBY 命令。

如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。

## hVals

命令返回哈希表所有的值。

一个包含哈希表中所有值的列表。 当 key 不存在时，返回一个空表。

```
redis 127.0.0.1:6379> HSET myhash field1 "foo"
(integer) 1
redis 127.0.0.1:6379> HSET myhash field2 "bar"
(integer) 1
redis 127.0.0.1:6379> HVALS myhash
1) "foo"
2) "bar"
```

## hGetAll

返回哈希表中，所有的字段和值。

在返回值里，紧跟每个字段名(field name)之后是字段的值(value)，所以返回值的长度是哈希表大小的两倍。

```
redis> HSET myhash field1 "Hello"
(integer) 1
redis> HSET myhash field2 "World"
(integer) 1
redis> HGETALL myhash
1) "field1"
2) "Hello"
3) "field2"
4) "World"
redis> 
```

## Evalsha

https://www.runoob.com/redis/scripting-evalsha.html

Redis Evalsha 命令根据给定的 sha1 校验码，执行缓存在服务器中的脚本。

将脚本缓存到服务器的操作可以通过 SCRIPT LOAD 命令进行。

这个命令的其他地方，比如参数的传入方式，都和 EVAL 命令一样。

# Eval

Redis Eval 命令使用 Lua 解释器执行脚本。

redis Eval 命令基本语法如下：

```lua
redis 127.0.0.1:6379> EVAL script numkeys key [key ...] arg [arg ...] 
```

参数说明：

- **script**： 参数是一段 Lua 5.1 脚本程序。脚本不必(也不应该)定义为一个 Lua 函数。
- **numkeys**： 用于指定键名参数的个数。
- **key [key ...]**： 从 EVAL 的第三个参数开始算起，表示在脚本中所用到的那些 Redis 键(key)，这些键名参数可以在 Lua 中通过全局变量 KEYS 数组，用 1 为基址的形式访问( KEYS[1] ， KEYS[2] ，以此类推)。
- **arg [arg ...]**： 附加参数，在 Lua 中通过全局变量 ARGV 数组访问，访问的形式和 KEYS 变量类似( ARGV[1] 、 ARGV[2] ，诸如此类)。

```lua
redis 127.0.0.1:6379> eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
1) "key1"
2) "key2"
3) "first"
4) "second"
```

# 缓存一致性

[(6 封私信 / 2 条消息) 如何保持mysql和redis中数据的一致性？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/319817091/answer/2110995185)

什么是缓存一致性? 只要数据库和缓存中的数据不一致就是存在缓存一致性问题, 不需要考虑异常的情况, 例如缓存删除成功了, 但是数据库没有更新成功, 我们认为是不存在缓存一致性问题, 用户访问缓存, 缓存不存在从数据库中重建缓存, 数据一致

##  出现原因

数据更新分为两步操作, 更新数据库和更新缓存

### 出现异常

更新数据库操作或更新缓存操作出现异常会导致一致性问题

先删除缓存, 后更新数据库可以避免

### 并发问题

更新数据的两步操作不是一个原子性操作, 线程A和线程B并行操作就会出现一致性问题

先更新数据库, 后删除缓存, 出现不一致问题的概率极低, 可以认为不会出现

## 解决办法

**先更新数据库, 后删除缓存**

更进一步，将删除缓存的操作放到mq中进行处理，确保删除缓存的操作一定能够成功执行

**订阅数据库变更日志**：实现sql级别的缓存，并订阅数据库变更日志（Binlog），根据Binlog去删除缓存

------

https://z.itpub.net/article/detail/524D9CAEFAA889660FF1A95DB4BB5B8E

- **强一致性**：这个一致性级别最符合用户的直觉，用户向系统中写入什么，读出的就会是什么，但会营销系统的性能。
- **弱一致性**：目前提报这里就是属于弱一致性的，不保证用户什么时候可以从缓存中读出最新的值，用户可以忍受数据不一致性。
- **最终一致性**：最终一致性是弱一致性的一个特例，系统会保证在一定时间内，能够达到一个数据一致的状态。这里之所以将最终一致性单独提出来，是因为它是弱一致性中非常推崇的一种一致性模型，也是业界在大型分布式系统的数据一致性上比较推崇的模型



# redis分布式锁

https://segmentfault.com/a/1190000037798450

**解锁还需加锁人：**释放分布式锁必须由添加分布式锁的线程来操作。我们可以在添加锁的时候给value设置一个uuid，加锁成功返回uuid，解锁时线程再拿着返回的uuid来验证解锁，防止当前客户端释放了别人加的锁

在Redis集群中，分布式锁的实现存在一些局限性，当主从替换时难以保证一致性。

## 加锁

```java
public class RedisTool {

    private static final String LOCK_SUCCESS = "OK";
    private static final String SET_IF_NOT_EXIST = "NX";
    private static final String SET_WITH_EXPIRE_TIME = "PX";

    /**
     * 尝试获取分布式锁
     * @param jedis Redis客户端
     * @param lockKey 锁
     * @param requestId 请求标识
     * @param expireTime 超期时间
     * @return 是否获取成功
     */
    public static boolean tryGetDistributedLock(Jedis jedis, String lockKey, String requestId, int expireTime) {

        String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);

        if (LOCK_SUCCESS.equals(result)) {
            return true;
        }
        return false;
    }
}
```

## 释放锁

```java
public class RedisTool {

    private static final Long RELEASE_SUCCESS = 1L;

    /**
     * 释放分布式锁
     * @param jedis Redis客户端
     * @param lockKey 锁
     * @param requestId 请求标识
     * @return 是否释放成功
     */
    public static boolean releaseDistributedLock(Jedis jedis, String lockKey, String requestId) {

        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId));

        if (RELEASE_SUCCESS.equals(result)) {
            return true;
        }
        return false;
    }
}
```

使用Lua脚本可以保证原子性

**保证原子性的原因：**线程A尝试释放锁，判断requestId是相等的，接着开始删除键值对，但是删除键值对的操作因为网络原因被阻塞住了，而刚好此时锁超时被删除了，另一个线程B获取到了锁，然后网络恢复线程A成功删除了锁，就把线程B获取到的锁给释放了。
