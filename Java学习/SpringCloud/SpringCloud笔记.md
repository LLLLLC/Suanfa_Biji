# 1. 基本知识

## 1.1 SpringCloud有哪些组件？

> - Nacos / Consoul 服务注册与发现
> - Ribbon 负债均衡
> - Feign 远程调用
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

```
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

### 1.3.1 Ribbon 负债均衡

为什么需要负债均衡呢?

- 如果有多个服务提供者，服务调用者如何知道究竟调用哪个服务呢？
- 而且服务调用者为何不用写死服务提供者的链接（ip和端口），只需要写服务名称即可？为什么我们只输入了服务名称就可以访问了呢？String url = "http://userservice/user/" + order.getUserId(); //由于已经在Eureka里面配置了服务，这里只需要写配置的服务名即可）

这都是Ribbon的负载均衡做到的，

- **针对问题一**，通过跟断点得知，Ribbon是通过几种不同的负载均衡算法实现的这一个机制（比如[轮询算法](https://blog.csdn.net/jasonliuvip/article/details/25725541)）；
- **针对问题二**，Ribbon会根据服务名称去Eureka注册中心拉取服务，如下两个图所示：

