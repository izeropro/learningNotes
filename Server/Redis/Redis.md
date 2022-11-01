## 1. Redis基本数据类型
    - 字符串（String）
    - 哈希（hash）
    - 列表（list）
    - 集合（set）
    - 有序集合（zSet）
    - HyperLogLog、流、地理坐标等
## 2. Redis高级功能
    - 消息队列、自动过期删除、事务、数据持久化、分布式锁、附近的人、慢查询分析、Sentinel和集群等
## 3. Redis命令
    - redis -cli -h 127.0.0.1 -p 6379
    - redis -cli shutdown nosave|save
    - exists keys
    - del keys
## 4. Redis结构
    - 单线程架构、I/O多路复用模型
    - 纯内存访问、避免了线程上下文切换带来的资源消耗和线程安全问题
## 5. Redis字符串最大不能超过512MB
## 6. Redis默认分16个数据库
## 7. ★★ Redis 持久化
    - RDB持久化
      - 把当前进程数据生成快照保存到硬盘的过程（以二进制方式写入磁盘）
      - 触发方式
        - 手动触发：save、bgsave
        - 自动触发：save x secends n、flushall
        - 主从同步
    - AOF持久化
      - 以独立的日志方式 把每次命令记录到aof文件中
      - AOF是否开启：config get appendonly
      - 开启AOF：1. config set appendonly 2. 配置文件：appendonly yes
      - 写入aof_buf缓冲区 -> AOF缓冲区根据对应策略持久化到磁盘
    - 混合持久化
      - redis4.0后
## 8. Redis事务
    - 一次执行多个命令，并且不会中断改而去执行其他客户端的命令
    - 三个阶段：开始事务 -> 命令入队 -> 执行事务
## 9. Redis内存溢出
    1. 命令行
        - 获取最大内存：config get maxmemory
        - 设置最大内存：config set maxmemory 1GB
    2. 内存溢出策略
       - noeviction：默认策略，数据永不过期，不会删除任何数据，当内存不足以容纳新写入数据时，新写入操作会报错，一般不推荐使用
       - volatile-lru：根据LRU算法删除设置了超时属性(expire)的键，直到腾出足够空间为止。如果没有可删除的键对象，回退到noeviction策略。这种情况一般是把 Redis 既当缓存，又做持久化存储的时候才用。
       - allkeys-lru：根据LRU算法删除键，不管数据有没有设置超时属性，直到腾出足够空间为止。一般推荐使用该策略
       - allkeys-random：内存不足以容纳新写入数据时，在键空间中，随机移除某个 Key。
       - volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 Key。
       - volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 Key 优先移除。如果没有，回退到noeviction策略。
## 10. Redis集群
    1. 主从模式：人工确定主从
    2. 哨兵模式：Sentinel哨兵监听，确定主从
    3. 集群模式：分布式存储，对数据分片，方便在线扩容
        - 槽（solt）范围：0 ~ 16383
## 11. Redis分布式锁
    1. 单机加锁：Redisson
        - 只要线程一加锁成功，就会启动一个watch dog看门狗，它是一个后台线程，会每隔10秒检查一下，如果线程1还持有锁，那么就会不断的延长锁key的生存时间。
        因此，Redisson就是使用Redisson解决了[锁过期释放，业务没执行完]问题。
    1. 集群加锁：Redlock算法
       - 多机实现的分布式锁Redlock+Redisson

## 12. Redis布隆过滤器
    1. 布隆过滤器是一种占用空间很小的数据结构，它由一个很长的二进制向量和一组Hash映射函数组成，它用于检索一个元素是否在一个集合中，空间效率和查询时间都比一般的算法要好的多，缺点是有一定的误识别率和删除困难。

## 13. 缓存穿透、缓存击穿、缓存雪崩

## 14. Redis延迟队列
    1. redis的过期key监控
    2. redis的zset实现延迟队列
        - 延迟消息队列的常见实现方式是通过 ZSet 的存储于查询来实现，它的核心思想是在程序中开启一个一直循环的延迟任务的检测器，用于检测和调用延迟任务的执行
    https://www.yht7.com/news/138710

## 15. Redis脑裂问题
    1. [sentinel 哨兵 - 脑裂处理方案](https://zhuanlan.zhihu.com/p/442519975)
    2. 脑裂主要表现为：同一个 redis 集群，原来的 master，经过故障转移后，出现多个 master
    3. 产生的问题：主库的数据还没有同步到从库，结果主库发生了故障，等从库升级为主库后，未同步的数据就丢失了
    4. 解决方案：主要通过 sentinel 哨兵的配置和 redis 的配置去解决问题。



【参考】  
[Redis](https://zhuanlan.zhihu.com/p/427496556)  