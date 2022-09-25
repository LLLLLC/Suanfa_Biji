# 1. Spring IOC

## 1.1 Spring Bean生命周期

参考：https://www.cnblogs.com/zrtqsk/p/3735273.html

> 主要是，实例化，属性注入，Aware接口感知，Postprocessor，init-method，Disposable，destory-method

Bean 的生命周期概括起来就是 **4 个阶段**：

1. 实例化（Instantiation）
2. 属性赋值（Populate）
3. 初始化（Initialization）
4. 销毁（Destruction）

Bean的完整生命周期经历了各种方法调用，这些方法可以划分为以下几类：

- 1、Bean自身的方法 ：这个包括了`Bean`本身调用的方法和通过配置文件中<bean>的init-method和destroy-method指定的方法

- 2、Bean级生命周期接口方法 ：这个包括了`BeanNameAware`、`BeanFactoryAware`、`InitializingBean`和`DiposableBean`这些接口的方法

- 3、容器级生命周期接口方法 ：这个包括了`InstantiationAwareBeanPostProcessor` 和 `BeanPostProcessor` 这两个接口实现，一般称它们的实现类为“后处理器”。

  > InstantiationAwareBeanPostProcessor 在最新的Spring中已经被放弃使用

- 4、工厂后处理器接口方法：这个包括了`AspectJWeavingEnabler`, `ConfigurationClassPostProcessor`, `CustomAutowireConfigurer`等等非常有用的工厂后处理器接口的方法。工厂后处理器也是容器级的。在应用上下文装配配置文件之后立即调用。

![image-20210821140404343](../images/1704860a4de235aa_tplv-t2oaga2asx-zoom-in-crop-mark_3024_0_0_0.jpg)

![image-20210821140404343](../images/181453414212066.png)

![image-20210821140404343](../images/181454040628981.png)

## 1.2 Spring Bean如何解决循环依赖



## 1.3 Spring BeanFactory

# 2. Spring AOP

## 2.1 AOP基本术语

| 术语              | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| 目标(Target)      | 被通知的对象                                                 |
| 代理(Proxy)       | 向目标对象应用通知之后创建的代理对象                         |
| 连接点(JoinPoint) | 目标对象的所属类中，定义的所有方法均为连接点                 |
| 切入点(Pointcut)  | 被切面拦截 / 增强的连接点（切入点一定是连接点，连接点不一定是切入点） |
| 通知(Advice)      | 增强的逻辑 / 代码，也即拦截到目标对象的连接点之后要做的事情  |
| 切面(Aspect)      | 切入点(Pointcut)+通知(Advice)                                |
| Weaving(织入)     | 将通知应用到目标对象，进而生成代理对象的过程动作             |

## 2.2 AOP 实现方式

AOP是基于动态代理的，因此，主要有以下几种实现方式

- JDK Proxy (基于接口)
- CJlib （基于子类）
- AspectJ 

## 2.3 AspcetJ 

> AspcetJ 好像既可以编译增加，也可以运行增强

###  2.3.1 AspcetJ 通知的类型有哪些？

- **Before**（前置通知）：目标对象的方法调用之前触发
- **After** （后置通知）：目标对象的方法调用之后触发
- **AfterReturning**（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- **AfterThrowing**（异常通知） ：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- **Around**： （环绕通知）编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法

### 2.3.2 Join Points （连接点）

Join Points可以看作是程序运行时的一个执行点，比如函数的调用，执行等。

| Join Points           | 说明                           | 示例                                 |
| --------------------- | ------------------------------ | ------------------------------------ |
| method call           | 函数调用                       | 比如调用Log.e()                      |
| method execution      | 函数执行                       | 比如Log.e()的执行内部                |
| constructor call      | 构造函数调用                   | 和method call类似                    |
| constructor execution | 构造函数执行                   | 和method execution类似               |
| field get             | 获取某个变量                   |                                      |
| field set             | 设置某个变量                   |                                      |
| pre-initialization    | Object在构造函数前做的一些工作 |                                      |
| initialization        | Object在构造函数中做的工作     |                                      |
| static initialization | 类初始化                       | 比如类的static{}                     |
| handler               | 异常处理                       | 比如try catch()中，对应catch内的执行 |

### 2.3.3 Pointcuts 连接点

一般情况下，使用@Pointcut注解的方法的方法体必须是空的，并且没有任何throws语句。如果切入点绑定了形式参数（使用args()、target()、this()、@args()、@target()、@this()、@annotation()），那么它们必须也是方法的形式参数。

> 修饰的方法body需要为空的

参数表达式：

举个例子

```java
@Pointcut("call(* *.*(..))")
void anyCall(){}

// 当要绑定参数的时候，只需要将参数作为被注解的方法的参数即可
@Pointcut("call(* *.*(int)) && args(i) && target(callee)")
void anyCall(int i, Fool callee){}
```

- call：处理Join Point的类型，例如call、execution

- 第一个`*`表示返回值，*表示返回值为任意类型

- 第二个`*.*`是匹配方法，`*`是进行通配，几个`*`没区别

- `android.app.Activity.on**`表示on开头的方法

- 可以通过`&&`、`||`、`!`来进行条件组合

- `()`代表方法参数，可以指定类型，例如android.os.Bundle，或者`(..)`这样来代表任意类型、任意个数的参数

  > 第一个参数，返回值， 第二个参数，切入方法，可以加Public等权限修饰符来修饰

### 2.3.4 Advice

Before、After、AfterReturning、AfterThrowing或者Around（等效于Before和After）等。

## 2.3 AspectJ 注解加载流程



## 2.4 过滤器和拦截器的区别



# 6. SpringSecurity

