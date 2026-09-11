# 基础篇
## 优点和缺点
### 优点
1. 应用解耦, 说人话: 就是之前一个订单业务是同步的, 如果要执行完毕, 就要一次执行库存系统、支付系统、物流系统等, 带来的问题就是, 延迟较高, 这个业务与其他业务高耦合, 如果你还要添加一个新的功能就要重新写一个业务加入到订单系统中
2. 异步提速, 同理一, 提高了速度
3. 削峰填谷, 也就是当一个**缓存**, 将用户的请求存储到mq中
![[Pasted image 20260910114044.png]]
![[Pasted image 20260910114404.png]]
![[Pasted image 20260910114618.png]]

### 缺点
![[Pasted image 20260910114734.png]]


## RocketMQ
![[Pasted image 20260910114952.png]]

![[Pasted image 20260910115908.png]]
### 单生产者单消费者, 这里略
### 单生产者多消费者
![[Pasted image 20260910162655.png]]
同一个group默认采用的是负载均衡模式,也就是轮训的方式, 如果希望consumer1和consumer2得到的消息一样,则需要,将其改为广播模式
![[Pasted image 20260910164238.png]]

### 多生产者模式 略

### 同步消息、异步消息、单向消息、延时消息、批量消息
![[Pasted image 20260910170023.png]]

![[Pasted image 20260910170011.png]]![[Pasted image 20260910170040.png]]
![[Pasted image 20260910170328.png]]
![[Pasted image 20260910170427.png]]

![[Pasted image 20260910170850.png]]
![[Pasted image 20260910170815.png]]

实现的借口分别为
![[Pasted image 20260910170148.png]]![[Pasted image 20260910170157.png]]![[Pasted image 20260910170202.png]]
![[Pasted image 20260910170455.png]]

### 消息过滤
#### Tag过滤
![[Pasted image 20260910171455.png]]

#### 配置过滤
![[Pasted image 20260910173512.png]]
这里记着要需要给 `broker.conf` 增加 `enablePropertyFilter=true`，然后重启常驻 Broker。
![[Pasted image 20260910173601.png]]![[Pasted image 20260910173612.png]]


### 消息的顺序
![[Pasted image 20260911111917.png]]
![[Pasted image 20260911111741.png]]
由于一个topic里面有多个队列,这就导致了一个完整的业务流程可能分布在不同的队列里面, 这样每一个队列的处理的线程就会导致结果不同, 因此我们希望对于一个业务来说应该前面的消息应该也要顺序完成才对