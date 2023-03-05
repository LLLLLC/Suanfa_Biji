# 1. 基本知识

参考：

B站黑马程序员 笔记  https://www.xn2001.com/archives/663.html

## 1.1 SpringCloud有哪些组件？

> - Nacos / Consoul 服务注册与发现
> - Ribbon 负债均衡
> - Feign 远程调用 / RestTemplate
> - GateWay 网关

## 1.2 服务注册

### 1.2.1 **Eureka**

> 目前，Eureka 1.X版本还在维护，但是2.X版本已经停止更新

**Eureka的作用：**

消费者该如何获取服务提供者具体信息?

- 服务提供者启动时向eureka注册自己的信息
- eureka保存这些信息
- 消费者根据服务名称向eureka拉取提供者信息

如果有多个服务提供者,消费者该如何选择?

- 服务消费者利用负载均衡算法，从服务列表中挑选一个

消费者如何感知服务提供者健康状态?

- 服务提供者会每隔30秒向EurekaServer发送心跳请求，报告健康状态
- eureka会更新记录服务列表信息，心跳不正常会被剔除
- 消费者就可以拉取到最新的信息

**注意点：**

Eureka自己也是一个微服务，Eureka启动时，要把自己也注册进去。这是因为如果后续搭建Eureka集群时做数据交流：

```yaml
server:
  port: 10086 # 服务端口
spring:
  application:
    name: eurekaserver # eureka的服务名称
eureka:
  client:
    service-url:  # eureka的地址信息
      defaultZone: http://127.0.0.1:10086/eureka
```

## 1.3 负债均衡

### 1.3.1 Ribbon 负载均衡

为什么需要负债均衡呢?

- 如果有多个服务提供者，服务调用者如何知道究竟调用哪个服务呢？
- 而且服务调用者为何不用写死服务提供者的链接（ip和端口），只需要写服务名称即可？为什么我们只输入了服务名称就可以访问了呢？String url = "http://userservice/user/" + order.getUserId(); //由于已经在Eureka里面配置了服务，这里只需要写配置的服务名即可）

这都是Ribbon的负载均衡做到的，

- **针对问题一**，通过跟断点得知，Ribbon是通过几种不同的负载均衡算法实现的这一个机制（比如[轮询算法](https://blog.csdn.net/jasonliuvip/article/details/25725541)）；
- **针对问题二**，Ribbon会根据服务名称去Eureka注册中心拉取服务，如下两个图所示：

> Ribbon 应该是在客户端实现的负载均衡

![3a63e86645fc4c5ea72fd8922a316023.png](../images/3a63e86645fc4c5ea72fd8922a316023.png)

![74da1efa07cc413fb31cf917dd7a0822.png](../images/74da1efa07cc413fb31cf917dd7a0822.png)

### 1.3.2 Ribbon 负载均衡策略

![971f44a376a6479ca683d716a9fcc2bc.png](../images/971f44a376a6479ca683d716a9fcc2bc.png)

![64db29d8532942a0a9d58fa22cc9661d.png](../images/64db29d8532942a0a9d58fa22cc9661d.png)

可以使用如下代码配置对某个服务的负载均衡策略(在 application.yml里配置)

```yaml
userservice: # 给某个微服务配置负载均衡规则，这里是userservice服务为例
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则
```

Ribbon默认是采用懒加载，即第一次访问时才会去创建LoadBalanceClient，请求时间会很长。

而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：

```yaml
ribbon:
  eager-load:
    enabled: true # 开启饥饿加载
    clients:
      - userservice # 指定饥饿加载的服务名称
      - xxxxservice # 如果需要指定多个，需要这么写
```

## 1.4 Nacos

> 使用nacos 时，需要注释掉Eureka相关的内容

### 1.4.1 Nacos 集群配置

默认的`ZoneAvoidanceRule`并不能实现根据同集群优先来实现负载均衡。

Nacos中提供了一个`NacosRule`的实现，可以优先从同集群中挑选实例。

1）给order-service配置集群信息

修改order-service的application.yml文件，添加集群配置：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```

2）修改负载均衡规则

修改order-service的application.yml文件，修改负载均衡规则

```yaml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule # 负载均衡规则 
```

配置完成之后，就可以实现同集群优先的 负载均衡了
