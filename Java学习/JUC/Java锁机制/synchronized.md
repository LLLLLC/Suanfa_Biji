# 1. 基础

主要知识点：

- Synchronized 原理
- Synchronized 锁升级过程
- 自旋锁

主要需要解决的问题：

- Synchronized 原理（Monitor，怎么锁住 Monitor的）
- 可重入原理
- 锁升级原理
- 自旋锁



## 1.1 Synchronized 原理

> Synchronized 是如何实现的？
>
> 基于C++实现，任何对象都有 monitor 监控状态，线程首先 通过 `monitorEnter`  将对象头通过CAS锁住，如果没有锁住，会两次自旋，不断锁升级。首先 偏向锁 检查 markDown 中的线程是否为自己，不是的话升级为轻量锁，持有锁的线程，会在栈帧中存储 markEWord 信息。再次抢夺锁的话，会升级为 重量级锁，进入等待队列。

### 1.1.1 Monitor

> Monitor 用来监控线程对象状态，每个对象都有 Monitor，Monitor 应该是用 C++写的

Monitor 对象结构：

<img src="C:\Users\karel\Desktop\算法笔记\Java学习\images\QQ截图20230421011000.png" alt="QQ截图20230421011000.png" style="zoom:50%;" />

其中关键的属性为：

- _WaitSet: 存放处于wait状态的线程队列
- _EntryList: 存放处于等待锁block状态的线程队列
- _recursions: 锁的重入次数
- _count:用来记录该线程获取锁的次数



## 1.2	基本用法

- 锁住实例
- 锁住类
- 修饰方法
- 锁住 this 