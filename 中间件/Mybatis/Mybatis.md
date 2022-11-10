# 1.Mybatis 基础

## 1.1 Mybatis 基本常识

### 1.1.1 传统JDBC操作数据库怎么办？

这里以传统JDBC编程中的查询操作为例进行说明，其主要步骤如下：

1. 注册数据库驱动类，明确指定数据库URL地址、数据库用户名、密码等连接信息。
2. 通过DriverManager打开数据库连接。
3. 通过数据库连接创建Statement对象。
4. 通过Statement对象执行SQL语句，得到ResultSet对象。
5. 通过ResultSet读取数据，并将数据转换成JavaBean对象。
6. 关闭ResultSet、Statement对象以及数据库连接，释放相关资源。

上述步骤1～步骤4以及步骤6在每次查询操作中都会出现，在保存、更新、删除等其他数据库操作中也有类似的重复性代码。在实践中，为了提高代码的可维护性，可以将上述重复性代码封装到一个类似DBUtils的工具类中。步骤5中完成了关系模型到对象模型的转换，要使用比较通用的方式封装这种复杂的转换是比较困难的。





### 1.1.1 Mybatis 是什么？

一个基于Java的持久层框架：

**持久层：** 可以将业务数据**存储到磁盘，具备长期存储能力**，只要磁盘不损坏，在断电或者其他情况下，重新开启系统仍然可以读取到这些数据。

**优点：** 可以**使用巨大的磁盘空间**存储相当量的数据，并且很**廉价**

**缺点：慢**（相对于内存而言）

> 通常持久层可以指数据库，更为广泛的说，就是存储在磁盘上的文件。

### 1.1.2 为什么使用Mybatis?

在我们**传统的 JDBC 中**，我们除了需要自己提供 SQL 外，还必须操作 Connection、Statment、ResultSet，不仅如此，为了访问不同的表，不同字段的数据，我们需要些很多雷同模板化的代码，闲的**繁琐又枯燥**。

而我们在使用了 **MyBatis** 之后，**只需要提供 SQL 语句就好了**，其余的诸如：建立连接、操作 Statment、ResultSet，处理 JDBC 相关异常等等都可以交给 MyBatis 去处理，我们的**关注点于是可以就此集中在 SQL 语句上**，关注在增删改查这些操作层面上。

并且 MyBatis 支持使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。

> 简单来说，就是为了简化JDBC的操作。

## 1.2 Mybatis的基本操作

1. 首先在Maven里面引入Mybatis和sql的依赖。
2. 配置mybatis-config.xml 文件，这个主要配置数据库连接方面的内容
3. 编写实体类
4. 编写Mapper接口
5. 编写xml文件
6. 其它问题，如maven静态资源过滤，config中mapper扫描目录等

<img src="../Java学习/images/QQ截图20211108180049.png" alt="QQ截图20211108180049.png" style="zoom:50%;" />

### 1.2.1 可能出错的地方

1. Mapper.xml 中需要对应接口和实体类。
2. Maven 静态资源过滤没有配置。
3. SQL连接的问题，如，时区，编码，对应数据库等。

## 1.3 Mybatis 缓存

### 1.3.1 什么是缓存 [ Cache ]？

- 存在内存中的临时数据。
- 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。

### 1.3.2 为什么使用缓存？

- 减少和数据库的交互次数，减少系统开销，提高系统效率。

> 连接数据库是十分消耗资源的，需要使用缓存来提高访问速度。

### 1.3.3 什么样的数据能使用缓存？

- 经常查询并且不经常改变的数据。

### 1.3.4 Mybatis缓存有哪些？

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

- MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**

- - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
  - 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

> 一级缓存，SqlSession,SqlSession关闭了之后就立即消失。
>
> 耳机缓存，对应Mapper。

### 1.3.5 一级缓存

一级缓存也叫本地缓存：

- 与数据库同一次会话期间查询到的数据会放在本地缓存中。
- 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；

### 1.3.6 一级缓存失效的几种情况

- sqlSession不同

  ```java
  @Test
  public void testQueryUserById(){
     SqlSession session = MybatisUtils.getSession();
     SqlSession session2 = MybatisUtils.getSession();
     UserMapper mapper = session.getMapper(UserMapper.class);
     UserMapper mapper2 = session2.getMapper(UserMapper.class);
  
     User user = mapper.queryUserById(1);
     System.out.println(user);
     User user2 = mapper2.queryUserById(1);
     System.out.println(user2);
     System.out.println(user==user2);
  
     session.close();
     session2.close();
  }
  ```

- sqlSession相同，查询条件不同

  ```java
  @Test
  public void testQueryUserById(){
     SqlSession session = MybatisUtils.getSession();
     UserMapper mapper = session.getMapper(UserMapper.class);
     UserMapper mapper2 = session.getMapper(UserMapper.class);
  
     User user = mapper.queryUserById(1);
     System.out.println(user);
     User user2 = mapper2.queryUserById(2);
     System.out.println(user2);
     System.out.println(user==user2);
  
     session.close();
  }
  ```

- sqlSession相同，两次查询之间执行了增删改操作！

  ```java
  @Test
  public void testQueryUserById(){
     SqlSession session = MybatisUtils.getSession();
     UserMapper mapper = session.getMapper(UserMapper.class);
  
     User user = mapper.queryUserById(1);
     System.out.println(user);
  
     HashMap map = new HashMap();
     map.put("name","kuangshen");
     map.put("id",4);
     mapper.updateUser(map);
  
     User user2 = mapper.queryUserById(1);
     System.out.println(user2);
  
     System.out.println(user==user2);
  
     session.close();
  }
  ```

  结论：**因为增删改操作可能会对当前数据产生影响**

- sqlSession相同，手动清除一级缓存

  ```java
  @Test
  public void testQueryUserById(){
     SqlSession session = MybatisUtils.getSession();
     UserMapper mapper = session.getMapper(UserMapper.class);
  
     User user = mapper.queryUserById(1);
     System.out.println(user);
  
     session.clearCache();//手动清除缓存
  
     User user2 = mapper.queryUserById(1);
     System.out.println(user2);
  
     System.out.println(user==user2);
  
     session.close();
  }
  ```

### 1.3.7 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存

- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；

- 工作机制

- - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
  - 新的会话查询信息，就可以从二级缓存中获取内容；
  - 不同的mapper查出的数据会放在自己对应的缓存（map）中；

### 1.3.8 如何开启二级缓存

1、开启全局缓存 【mybatis-config.xml】

```xml
<setting name="cacheEnabled" value="true"/>
```

2、去每个mapper.xml中配置使用二级缓存，这个配置非常简单；【xxxMapper.xml】

```xml
<cache/>

官方示例=====>查看官方文档
<cache
 eviction="FIFO"
 flushInterval="60000"
 size="512"
 readOnly="true"/>
这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。
```

3、代码测试

- 所有的实体类先实现序列化接口
- 测试代码

```java
@Test
public void testQueryUserById(){
   SqlSession session = MybatisUtils.getSession();
   SqlSession session2 = MybatisUtils.getSession();

   UserMapper mapper = session.getMapper(UserMapper.class);
   UserMapper mapper2 = session2.getMapper(UserMapper.class);

   User user = mapper.queryUserById(1);
   System.out.println(user);
   session.close();

   User user2 = mapper2.queryUserById(1);
   System.out.println(user2);
   System.out.println(user==user2);

   session2.close();
}
```

### 1.3.9 二级缓存的特点

- 只要开启了二级缓存，我们在同一个Mapper中的查询，可以在二级缓存中拿到数据
- 查出的数据都会被默认先放在一级缓存中
- 只有会话提交或者关闭以后，一级缓存中的数据才会转到二级缓存中

<img src="../Java学习/images/微信图片_20211109113932.jpg" alt="微信图片_20211109113932.jpg" style="zoom: 67%;" />

## 1.7 ${}和#{} 复制方式有什么不同之处

${}的本质就是字符串拼接，#{}的本质就是占位符赋值  

${}使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号；但是#{}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号

> 在JDBC中，动态写SQL语句有两种方式
>
> - 一种是直接在SQL语句里面，直接通过字符串拼接组成SQL语句
> - 另一种通过预编译，生成带有？的语句，然后再写入进去
