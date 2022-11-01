# Map
   
## TreeMap
    基于红黑树实现
## HashMap
1. 基于哈希表实现:  
   * 如果O1和O2的hash值相同，就会存放到同一个单向链表上，
如果不同，但由于哈希算法执行结束之后转换的数组下标可能相同，此时会发上“哈希碰撞”。
   * JDK8之后,如果哈希表单向链表中元素超过8个，那么单向链表这种数据结构会变成红黑树数据结构。当红黑树上的节点数量小于6个，会重新把红黑树变成单向链表数据结构。 
   * 红黑树参考：[红黑树之原理和算法详细介绍](https://www.cnblogs.com/skywang12345/p/3245399.html)

2. key的hash值: key的hashcode值与hashcode的右移16位进行异或  
 参考：[HashMap解析：hash(key)](https://blog.csdn.net/weixin_38950807/article/details/88334665)
3. **HashMap源码&底层数据结构分析**  
参考: [javaguide的hashmap-简介](https://javaguide.cn/java/collection/hashmap-source-code.html)
4. LinkedHashMap  
使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序

### ConcurrentHashMap(线程安全)  
1. 和 HashMap 实现上类似，最主要的差别是 ConcurrentHashMap 采用了分段锁（Segment），每个分段锁维护着几个桶（HashEntry），多个线程可以同时访问不同分段锁上的桶，从而使其并发度更高（并发度就是 Segment 的个数）
2. **源码实现**
   - JDK1.7
     - 分段锁机制实现
     - 结构  
        * 数组（Segment） + 数组（HashEntry） + 链表（HashEntry节点）
        * Segment 数组结构：Segment 类继承于 ReentrantLock 类，从而使得 Segment 对象能充当锁的角色
        * HashEntry数组结构组成：典型的四元组 > 分别是key、hash、value和next;在HashEntry类中，key，hash和next域都被声明为final的，value域被volatile所修饰，因此HashEntry对象几乎是不可变的，这是ConcurrentHashmap读操作并不需要加锁的一个重要原因
        * put方法中加锁
   - JDK1.8
     - 使用的是优化的synchronized 关键字 和 cas操作维护并发
     - 结构
        * Node数组+链表 / 红黑树
        * put方法先判断是否需要初始化 还是其他线程正在扩容，若都不满足，则synchronized关键字修饰代码块进行插入
3. **ConcurrentHashMap源码&底层数据结构分析**  
   参考：[ConcurrentHashMap源码&底层数据结构分析](https://javaguide.cn/java/collection/concurrent-hash-map-source-code.html)
## HashTable(了解)
1. 和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程同时写入 HashTable 不会导致数据不一致。它是遗留类，不应该去使用它，而是使用 ConcurrentHashMap 来支持线程安全，ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。
   