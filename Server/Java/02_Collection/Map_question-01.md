参考：[最通俗易懂搞定HashMap的底层原理](https://zhuanlan.zhihu.com/p/79507868)
## HashMap
1. HashMap的工作原理是什么？
2. HashMap中的“死锁”是怎么回事？  
    >HashMap是非线程安全，死锁一般都是产生于并发情况下。我们假设有二个进程T1、T2，HashMap容量为2,T1线程放入key A、B、C、D、E。在T1线程中A、B、C Hash值相同，于是形成一个链接，假设为A->C->B，而D、E Hash值不同，于是容量不足，需要新建一个更大尺寸的hash表，然后把数据从老的Hash表中 迁移到新的Hash表中(refresh)。这时T2进程闯进来了，T1暂时挂起，T2进程也准备放入新的key，这时也 发现容量不足，也refresh一把。refresh之后原来的链表结构假设为C->A，之后T1进程继续执行，链接结构 为A->C,这时就形成A.next=B,B.next=A的环形链表。一旦取值进入这个环形链表就会陷入死循环。
3. HashMap中能put两个相同key吗？为什么？
4. HashMap中的键值可以为null吗？原理？
5. HashMap扩容机制？
### HashMap关于key的hash值
1. 为什么不直接采用经过hashCode（）处理的哈希码作为存储数组table的下标位置？  
    >因为直接算出来的hashCode值是2进制的32位数，要是直接使用这个数的话，就可能会出现hashCode的值远远大于数组实际元素个数，这样节点分布就会极不均匀，并且也浪费了大量的空间，所以，采用哈希码与运算(&)（数组长度-1）计算数组下标。
2. 为什么采用哈希码与运算(&)（数组长度-1）计算数组下标？
3. 为什么在计算数组下标前，需对哈希码进行二次处理：扰动处理？