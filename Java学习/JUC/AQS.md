# 1. LockSupport

1.1 park() / park(Object blocker)

阻塞当前线程/阻塞传入的具体线程

## 1.2 unpark(Thread thread)

唤醒处于阻塞状态的指定线程

> park 和 unpark 先后顺序可以不一致

Q: 为什么可以先唤醒线程后阻塞线程？

因为unpark获得了一个凭证，在之后再调用park方法，就可以名正言顺的消费，因此不会阻塞

# 2. AQS

## 2.1 前置知识

- 公平锁和非公平锁
- 可重入锁
- LockSupport
- 自旋锁
- 数据结构
- 模板模式

## 2.2 AQS 延申

- AbstractOwnableSynchronizer
- AbstractQueuedLongSynchronizer
- AbstactQueuedSynchronizer 抽象队列同步器

## 2.3 简介

AQS 是用来构建锁或者其它同步器组件的重量级基础框架及整个JUC体系的基石， 通过内置的FIFO队列来完成资源获取线程的排队工作，并通过一个int类变量（state）表示持有锁的状态

CLH：Craig、Landin and Hagersten 队列，是一个双向链表，AQS中的队列是CLH变体的虚拟双向队列FIFO

![90f4132ca8188e1af37bed13615a8b37.png](../images/90f4132ca8188e1af37bed13615a8b37.png)

