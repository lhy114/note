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
