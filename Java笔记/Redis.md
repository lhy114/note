# 基础篇
## NoSQL

![[Pasted image 20260824103138.png]]

## Redis基础知识
![[Pasted image 20260824103643.png]]

### 基本命令
- Homebrew 安装：`/opt/homebrew/opt/redis`
- 后台服务已启动，并设为登录时自动启动
- 连通性测试：`redis-cli ping` 返回 `PONG`

redis-cli                    # 进入命令行客户端
brew services start redis    # 启动
brew services stop redis     # 停止
brew services list           # 查看状态

#### Redis的数据结构
![[Pasted image 20260824142435.png]]
这里就要像一个问题,如果说我们存储的数据是一个对象怎么半呢? 这个时候jdk提供了一个 **序列化** 以及 第三方库的**toJSON** .


#### 通用命名
![[Pasted image 20260824143548.png]]
expire:-1 表示永远, -2 过期被删除了.

#### String
![[Pasted image 20260824143724.png]]
![[Pasted image 20260825094826.png]]
#### key的层级结构
![[Pasted image 20260825095517.png]]


#### Hash类型
我的理解就是json是一个字符串,如果你要进行修改很麻烦,那么既然json类似于一个hashmap的结构,为什么我们不把value在分为一个hashmap呢
![[Pasted image 20260825095810.png]]
![[Pasted image 20260825095824.png]]

#### LIst 类型
![[Pasted image 20260825100422.png]]
![[Pasted image 20260825100613.png]]

#### Set类型
![[Pasted image 20260825101041.png]]
![[Pasted image 20260825101411.png]]

#### SortedSet类型
![[Pasted image 20260825101715.png]]![[Pasted image 20260825101845.png]]
注意：所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV即可

### Redis客户端
![[Pasted image 20260825102702.png]]
#### jedis
![[Pasted image 20260825105633.png]]
![[Pasted image 20260825105619.png]]
##### jedis连接池
![[Pasted image 20260825105603.png]]

#### Spring Data Redis
Spring 默认是lettuce客户端
![[Pasted image 20260825111603.png]]![[Pasted image 20260825111809.png]]![[Pasted image 20260825111856.png]]![[Pasted image 20260825112005.png]]![[Pasted image 20260825112015.png]]

##### 默认序列化方式
![[Pasted image 20260825112550.png]]

![[Pasted image 20260825112738.png]]![[Pasted image 20260825113112.png]]
反序列化的时候,能自动同字节码文件获得对应的类型

##### StringRedisTemplate
![[Pasted image 20260825113240.png]]
![[Pasted image 20260825113333.png]]![[Pasted image 20260825113429.png]]![[Pasted image 20260825113631.png]]


# Redis作为缓存
## 替换策略
![[Pasted image 20260827115146.png]]![[Pasted image 20260827115226.png]]![[Pasted image 20260827115344.png]]

## 更新策略
![[Pasted image 20260827115707.png]]

![[Pasted image 20260827120129.png]]

缓存一致性无法做到绝对一致，只能选择一个概率更低、恢复成本更低的方案. 这里为什么第二个比较好, 第一个方案结束之后, DB里面是:new, redis里面是: old; 但是第二个呢: DB里面是: new, redis:空. 也就是第二个方案他能够保障我只有仅有在redis为空和update之前这段时间是错误的了,后续的查询是正确的; 但是第一个方案, 后续的查询仍然是错误的,造成的严重性更大. 

**先更新数据库再删除缓存的优势在于，最终状态一定会落到“数据库最新，缓存为空”的可恢复状态；而先删除缓存再更新数据库可能因为并发查询导致旧数据重新进入缓存，最终形成“数据库最新，缓存错误”的长期不一致状态。**
![[Pasted image 20260827120148.png]]
## 缓存穿透(spring cloud)
![[Pasted image 20260827144949.png]]![[Pasted image 20260827145037.png]]![[Pasted image 20260827145444.png]]

## 缓存雪崩(spring cloud)
![[Pasted image 20260827151657.png]]

## 缓存击穿
![[Pasted image 20260827152528.png]]
### 互斥锁
![[Pasted image 20260827152546.png]]
![[Pasted image 20260827152732.png]]


### 逻辑锁

![[Pasted image 20260827152552.png]]
![[Pasted image 20260827163049.png]]
![[Pasted image 20260827152600.png]]


# Redis 作为一个高性能的原子计数器。
## 秒杀问题
### 全局ID生成器
![[Pasted image 20260827201731.png]]![[Pasted image 20260827201943.png]]
这不就是雪花算法吗?
![[Pasted image 20260819143242.png]]雪花算法（Snowflake）是用来**生成全局唯一、趋势递增的分布式 ID** 的，典型用途就是分布式系统里的主键。

**为什么需要它**

单机数据库可以用自增 ID，但分布式系统里多个服务、多台机器各自生成 ID，容易冲突。雪花算法让不同机器各自生成 ID 也不会重复，同时还能保证大致有序。

**ID 的构成（64 位 Long）**

```
1位符号位 | 41位时间戳 | 10位机器ID | 12位序列号
```

- 符号位：固定 0。
- 时间戳：记录生成时间，保证 ID 随时间递增。
- 机器 ID：区分不同机器，防止跨机器冲突。
- 序列号：同一毫秒内最多生成 4096 个 ID，超出则等下一毫秒。

**特点**

- 全局唯一，不依赖数据库。
- 趋势递增，比 UUID 更有利于数据库索引，尤其 InnoDB 主键。
- 生成速度极快，高并发下每毫秒每台机器可生成几千个。
- 纯内存计算，不需要网络交互。

|1|雪花算法|Redis ID|
|---|---|---|
|生成位置|本机|Redis服务器|
|依赖网络|❌|✅|
|性能|极高|较高|
|实现难度|较复杂|简单|
|唯一性|机器ID保证|Redis单线程保证|
|是否依赖中间件|否|Redis|
|是否趋势递增|是|是|
|适合大规模|非常适合|一般|
![[Pasted image 20260828095247.png]]