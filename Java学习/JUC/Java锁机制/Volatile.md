## Volatile 三个特点

- 不保证原子性

- 保证变量的可见性

- 防止指令重排序

  > 主要基于内存屏障 loadload loadstore storestore
  >
  > 需要进一步细化

## 主要用途

- DCL 双重校验单例模式

  > instance = new SingletonDemo 实例的创建过程
  >
  > 1. 分配内存空间
  >
  > 2. 初始化对象
  >
  > 3. 设置对象头
  >
  >    其中步骤2，3顺序可能会发生变化，导致对象头

- CAS 操作