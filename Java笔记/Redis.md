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
![[Pasted image 20260827115146.png]]![[Pasted image 20260827115226.png]]![[Pasted image 20260827115344.png]]![[Pasted image 20260827115707.png]]