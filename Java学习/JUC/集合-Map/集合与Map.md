## 1. 常见的集合种类

基本划分：

一般划分：

- Collection
- Map

Map进一步划分：

- `HashMap (C)` ==> `LinkedHashMap(C)`

- `ConcurrentMap(I)` ==> `ConcurrentHashMap(C)`

  ​						  ==> `ConcurrentNavigableMap (I)` ==> `ConcurrentSkipListMap(C)`

- `SortedMap(I)` ==> `NavigableMap(I)` ==> `TreeMap(C)`/ `ConcurrentNavigableMap(I)`

- `Hashtable` 其继承了 `Dictionary` （独立的抽象类） 

## 2. ConcurrentSkipListMap

跳表是一种可以用来快速查找的数据结构，有点类似于平衡树。它们都可以对元素进行快速的查找。但一个重要的区别是：对平衡树的插入和删除往往很可能导致平衡树进行一次全局的调整。而对跳表的插入和删除只需要对整个数据结构的局部进行操作即可。这样带来的好处是：在高并发的情况下，你会需要一个全局锁来保证整个平衡树的线程安全。而对于跳表，你只需要部分锁即可。

> 总结，不像红黑树那样增加元素需要左旋或者变色等操作，这些操作可能会锁住整个树。
>
> 而跳表只需要锁住局部的区域。猜测，因此TreeMap 不能并发执行，而 `ConcurrentSkipListMap` 可以。

`ConcurrentSkipListMap` 可以参考：

https://www.geeksforgeeks.org/concurrentskiplistmap-in-java-with-examples/

跳表：

![8z9ioz2hll.png](C:\Users\karel\Desktop\算法笔记\Java学习\images\8z9ioz2hll.png)

![ConcurrentSkipListMap-in-Java-with-Examples](C:\Users\karel\Desktop\算法笔记\Java学习\images\ConcurrentSkipListMap-in-Java-with-Examples.png)

> 底层实现方式跳表
>
> 底层使用 CAS  `UNSAFE.compareAndSwapObject(this, valueOffset, cmp, val)`

内部数据结构：

![m4qao5wdm.png](C:\Users\karel\Desktop\算法笔记\Java学习\images\m4qao5wdm.png)

ConcurentSkipListMap 的经典应用：（消息队列异步确认）

```java
/**
 * 确认收到消息的一个回调
 * 1.消息序列号
 * 2.true 可以确认小于等于当前序列号的消息（此处感觉应该是大于，因为序号是从小到大来排序的）
 * false 确认当前序列号消息
 */
ConfirmCallback ackCallback = (sequenceNumber, multiple) -> {
    if (multiple) {
        //返回的是小于（大于？）等于当前序列号的未确认消息 是一个 map
        ConcurrentNavigableMap<Long, String> confirmed =
                outstandingConfirms.headMap(sequenceNumber, true); // 会保留截取出去的部分，返回引用
        //清除该部分未确认消息
        confirmed.clear();
    } else {
        //只清除当前序列号的消息
        outstandingConfirms.remove(sequenceNumber);
    }
};
```

