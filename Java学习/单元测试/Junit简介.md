## 2. 常见注解

可以参考：https://blog.csdn.net/Dream_Weave/article/details/83859883

- @Before
- @After
- @BeforeClass
- @AfterClass

### 2.1 Before

被@BeforeClass注解的方法会是：

- 只被执行一次
- 运行junit测试类时第一个被执行的方法

这样的方法被用作执行计算代价很大的任务，如打开数据库连接。被@BeforeClass 注解的方法应该是静态的（即 static类型的）。

### 2.2 After

被@AfterClass注解的方法应是：

- 只被执行一次
- 运行junit测试类是最后一个被执行的方法

该类型的方法被用作执行类似关闭数据库连接的任务。被@AfterClass 注解的方法应该是静态的（即 static类型的）。

### 2.3 BeforeClass

被@BeforeClass注解的方法会是：

- 只被执行一次
- 运行junit测试类时第一个被执行的方法

这样的方法被用作执行计算代价很大的任务，如打开数据库连接。被@BeforeClass 注解的方法应该是**静态的（即 static类型的）。**

### 2.4 AfterClass

被@AfterClass注解的方法应是：

- 只被执行一次
- 运行junit测试类是最后一个被执行的方法

该类型的方法被用作执行类似关闭数据库连接的任务。被@AfterClass 注解的方法应该是**静态的（即 static类型的）。**

