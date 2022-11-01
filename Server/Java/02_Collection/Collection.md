# Set
    仅接收一次，不可重复，并做内部排序
## HashSet
1. 使用hash表（数组）存储元素，即基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的
2. LinkedHashSet  
    具有 HashSet 的查找效率，并且内部使用双向链表维护元素的插入顺序
## TreeSet  
1. 底层实现为二叉树，元素排好序，即基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)

****
# List
## ArrayList
1. 基于动态数组实现，支持随机访问
2. **ArrayList源码&扩容机制分析**
参考: [javaguide的arraylist-简介](https://javaguide.cn/java/collection/arraylist-source-code.html)
3. Vector（了解）  
    和 ArrayList 类似，但它是线程安全的，使用synchronized关键字进行同步，实现线程安全
4. ArrayList 对比 Vector  
    线程安全的 ArrayList：
        List<String> list = new ArrayList<>();
        List<String> synList = Collections.synchronizedList(list);
        List<String> list = new CopyOnWriteArrayList<>();
## LinkedList
    1. 基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素；
       还可以用作栈、队列和双向队列。
    2. 线程非安全问题：
       - 数据被覆盖、栈溢出等
       - 双向链表变成循环链表

# Queue
1. LinkedList
    可以用它来实现双向队列
2. PriorityQueue
    基于堆结构实现，可以用它来实现优先队列