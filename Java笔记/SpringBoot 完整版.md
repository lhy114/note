# 基础篇
## Spring 与 Spring Boot之间的对比（参考SSM）
![[Pasted image 20260820105101.png]]

## 入门案例
你早就熟练了，这里不讲述

### POM.xml
![[Pasted image 20260820110141.png]]
这里通过依赖继承的方法是的Spring-boot对包的依赖很简单
![[Pasted image 20260820110923.png]]

### 引导类
![[Pasted image 20260820111310.png]]

### 思想？
**这里我认为Spring的核心思想是，只要是java写的类，需要统一管理，那么就可以放在spring的IOC容器中**

## 基础配置
### 文件格式（常用的是yml  ）
如果文件提示消失查看这个视频 https://www.bilibili.com/video/BV15b4y1a7yG?spm_id_from=333.788.player.switch&vd_source=43668b2556859a05028a20161f3f22c6&p=20

**文件的优先级为：** properties > yml > yaml. 不同配置文件中相同配置按照加载优先级相互覆盖，不同配置文件中不同配置全部保留
![[Pasted image 20260820113327.png]]

### yaml的数据读取

![[Pasted image 20260820114807.png]]![[Pasted image 20260820114933.png]]![[Pasted image 20260820115032.png]]![[Pasted image 20260820115332.png]]

记忆点：封装一个对象，你肯定首先要交给spring进行管理，那么此时就要用component进行注解，然后给出配置文件，所以此时需要configurationproperties

## 整合第三方技术
我个人认为的技术：
1. 导入对应的pom坐标
2. 配置相关属性
3. 交给Spring进行管理，如果这个技术没有提供第三方的注解，你需要手动的将其利用@component技术将其交给spring进行管理，也就是说需要自己写一个util类来进行实现。甚至自己也可以写一个自定义注解方便后期管理。
4. 注意如果是第三方无法

## 基础的业务实现（不是特别难，唯一需要注意的是，如果想要偷懒利用MP自动生成，请看MP）
### 表现层
### 业务层
### 数据访问层
![[Pasted image 20260820153509.png]]

# 运维使用篇
## 打包和快速启动
![[Pasted image 20260820162042.png]]

### 打包之后的文件夹（使用maven插件）
会导入源代码、依赖的jar包，以及spring启动以来的文件
![[Pasted image 20260820162416.png]]

![[Pasted image 20260820162528.png]]

### 临时属性配置
![[Pasted image 20260820164044.png]]
![[Pasted image 20260820164527.png]]
### 属性加载顺序
![[Pasted image 20260820164117.png]]

### 配置文件分类
![[Pasted image 20260820164949.png]]

### 自定义配置文件
![[Pasted image 20260820165414.png]]![[Pasted image 20260820165423.png]]

### 多环境配置
![[Pasted image 20260820165839.png]]![[Pasted image 20260820170016.png]]![[Pasted image 20260820170432.png]]

### 利用maven控制springboot环境
![[Pasted image 20260820170750.png]]![[Pasted image 20260820170810.png]]

### 日志利用 sli4j和lombok
![[Pasted image 20260820171453.png]]![[Pasted image 20260820171608.png]]![[Pasted image 20260820172130.png]]

### 记录日志文件
![[Pasted image 20260820172749.png]]

# 高级开发
## 热部署
程序在运行过程中，如果修改一部分功能业务可以在不重启程序的情况就行修改。对与一个启动需要执行重启和重载两个步骤，但是在热部署过程不会执行重载的过程。![[Pasted image 20260821095937.png]]

![[Pasted image 20260821095730.png]]

### 热部署的范围
![[Pasted image 20260821101147.png]]![[Pasted image 20260821101155.png]]

## 配置高级
### 第三方bean的绑定
![[Pasted image 20260821102200.png]]

![[Pasted image 20260821102406.png]]

这里要强调的是enable注解的作用，它主要是将指定的configuration注解的类放到spring容器中进行统一管理。因此，如果你在configuration的类上面加了一个component或者已经声明他是一个bean对象了，此时就会出现bean不唯一的冲突。

### 宽松绑定
![[Pasted image 20260821102956.png]]
注意@Value注解不行
![[Pasted image 20260821103203.png]]

### 常用计量单位
![[Pasted image 20260821103952.png]]

### 对Bean数据进行校验
JSR303是一个规范接口，hibernate是对应的实现类
![[Pasted image 20260821104609.png]]
![[Pasted image 20260821104655.png]]![[Pasted image 20260821104701.png]]
## 测试
### 加载测试时候的专用属性
![[Pasted image 20260821112849.png]]![[Pasted image 20260821112920.png]]

### 测试的时候加入一个临时的Bean
![[Pasted image 20260821113213.png]]
其实没有必要，只要你导入的第三方Bean是位于当前包及其子包下也能够被找到的，但是还是建议你加上去。

### Web环境模拟测试
最主要的是首先你要开启MVC的虚拟测试,然后注入虚拟的MVC对象,最后执行结果与预期值进行对比.
你要比什么,你就通过匹配器去找什么,然后用复制粘贴去填写数据
![[Pasted image 20260822101409.png]]
![[Pasted image 20260821115043.png]]

#### 虚拟请求
![[Pasted image 20260821115529.png]]
#### 虚拟请求状态匹配
![[Pasted image 20260822101207.png]]
#### 虚拟请求体匹配
![[Pasted image 20260822101411.png]]

![[Pasted image 20260822101657.png]]

### 业务层测试数据回滚
你在测试的时候,写入到数据库里面的数据肯定不能留存,所以要进行回滚.
在正常业务代码中，`@Transactional` 管理数据库事务：方法成功完成时提交，发生符合回滚规则的异常时回滚；而在 Spring 测试中，`@Transactional` 除了开启事务外，Spring Test 默认会在测试结束后回滚该事务，因此测试产生的数据不会持久化到数据库。

![[Pasted image 20260822102503.png]]![[Pasted image 20260815161216.png]]

```
                @Transactional
                      │
          ┌───────────┴───────────┐
          │                       │
      正常业务运行              Spring Test
          │                       │
      开始事务                  开始事务
          │                       │
     执行数据库操作            执行数据库操作
          │                       │
       方法结束                测试结束
          │                       │
    ┌─────┴─────┐                  │
    │           │                  │
  成功        异常                 │
    │           │                  │
 COMMIT      ROLLBACK           ROLLBACK
    │
真正写入数据库
```

### 随机测试数据
![[Pasted image 20260822103555.png]]


## 数据层
这里我们首先回顾一下传统的数据层操作方式: MySql----MySql Driver----JDBC----Java
**JDBC**：Java 标准库里的接口，定义在 `java.sql` / `javax.sql` 包里。它只是“规范”，自己不连数据库。它规定了 `Connection`、`Statement`、`ResultSet`、`DataSource` 这些接口长什么样。


```
原始的调用方法
String url = "jdbc:mysql://localhost:3306/shop";
try (Connection conn = DriverManager.getConnection(url, "root", "123456");
     PreparedStatement ps = conn.prepareStatement("select * from user where id = ?")) {
    ps.setInt(1, 1);
    try (ResultSet rs = ps.executeQuery()) {
        while (rs.next()) {
            System.out.println(rs.getString("name"));
        }
    }
}
```
你可以看到,我们需要通过- `DriverManager.getConnection()` 每次都新建一条 TCP 连接，还要做认证，非常贵。然后 打开、关闭 `Connection`、`Statement`、`ResultSet` 全靠手动，漏了`close()` 就会把连接和资源耗尽。

因此为了解决这样复杂连接过程,**人们希望有一个统一管理的东西来解决数据库连接的问题**, 因此提出了datasource(我tm不知道为什么要翻译成数据库连接池).`DataSource` / 连接池：提前准备一批连接放在池子里，要用就借，用完还回去。TCP 连接、认证握手都只做一次，后续复用，性能差距非常大。
![[Pasted image 20260822110543.png]]

### SQL
#### Spring 内部的数据源
![[Pasted image 20260822111338.png]]
![[Pasted image 20260822111416.png]]

#### Spring内部的持久化层----jdbc template
![[Pasted image 20260822111548.png]]![[Pasted image 20260822112259.png]]

#### Spring内置数据库
![[Pasted image 20260822113454.png]]![[Pasted image 20260822113517.png]]
![[Pasted image 20260822113634.png]]
![[Pasted image 20260822113528.png]]

### NoSQL
#### Redis(请参考Redis学习笔记,有一个实验请你在Redis的时候做一下)
![[Pasted image 20260822113919.png]]
![[Pasted image 20260822115122.png]]![[Pasted image 20260822115114.png]]
![[Pasted image 20260822115143.png]]
![[Pasted image 20260822115203.png]]
![[Pasted image 20260822115947.png]]

在spring-boot里面,如果我要通过RedisTemplate进行操作(假设不指定范性,默认为object),那么进行的put、get等操作,通过redis-cli是查不到的; 然后给出的资料说的是利用StringRedisTemplate就行了,因为redis-cli是以字符串的形式操作的,所以我想问的是,能不能用RedisTemplate<String, String>代替StringRedisTemplate呢?

能，但有个前提：**只写 `RedisTemplate<String, String>` 是不够的，必须把序列化器也配成 `StringRedisSerializer`**。

原因就是刚才说的：泛型 `<String, String>` 只影响 Java 编译期，不改变运行时行为。如果你只是这样写：

```
@Autowired
private RedisTemplate<String, String> redisTemplate;
```

默认情况下它的序列化器还是 `JdkSerializationRedisSerializer`，写入 Redis 时字符串照样会被 Java 序列化成二进制，key 会带 `\xac\xed` 前缀，redis-cli 里依然查不到。

要让 `RedisTemplate<String, String>` 真正替代 `StringRedisTemplate`，需要这样配置：

```
@Bean
public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory factory) {
    RedisTemplate<String, String> template = new RedisTemplate<>();
    template.setConnectionFactory(factory);

    StringRedisSerializer serializer = new StringRedisSerializer();
    template.setKeySerializer(serializer);
    template.setValueSerializer(serializer);
    template.setHashKeySerializer(serializer);
    template.setHashValueSerializer(serializer);

    template.afterPropertiesSet();
    return template;
}
```

##### jedis和lettuce
![[Pasted image 20260822121040.png]]

#### Mongodb(参考Mongodb文档)
![[Pasted image 20260822142014.png]]
主要针对临时存储或者永久性存储,主要针对的是修改频率很高的场景
![[Pasted image 20260822143739.png]]

#### ES(同理看ES文档,没听懂)
![[Pasted image 20260822144253.png]]

索引:id建立索引,然后id索引查询对应的数据
倒排索引:通过数据建立一个到id的索引
文档:ES首先通过倒排索引(查询的字段)找到对应的id关键字,然后通过id关键字找到对应的数据(注意这里的数据一般不是Mysql存储的对应id的全部信息).
如果此时用户要获得全部信息,那么查询mysql数据库,得到完整的信息
![[Pasted image 20260822151135.png]]

这里注意spring官方给的是一个低版本的,如果需要高版本的,需要手工导入
![[Pasted image 20260822151217.png]]![[Pasted image 20260822151239.png]]![[Pasted image 20260822151301.png]]

## 整合第三方技术
### 缓存
这里想要提及到一点,就是cache可以做临时的数据存储,也就是我要生产一个短信
![[Pasted image 20260822165630.png]]

#### Spring的缓存
![[Pasted image 20260822172043.png]]![[Pasted image 20260822172051.png]]![[Pasted image 20260822172055.png]]![[Pasted image 20260822200504.png]]

**`@Cacheable`：先查缓存，命中就不执行方法**

- 请求进来先按 `value` + `key` 找缓存。
- 有缓存：直接返回缓存里的值，方法体不执行，所以不会走 SQL。
- 没缓存：执行方法，把返回值写进缓存，下次就能命中。
- 典型用法：查询方法，比如你现在的 `getById`。

**`@CachePut`：每次都执行方法，执行完再写缓存**

- 不判断缓存是否命中，方法体每次都执行。
- 执行完把返回值写入缓存，覆盖旧值。
- 典型用法：更新操作后主动刷新缓存，保证缓存里的数据是最新的。


#### Ehcache
![[Pasted image 20260822202639.png]]![[Pasted image 20260822202654.png]]![[Pasted image 20260822202704.png]]


#### Redis
内存数据库 vs 缓存,核心判断标准：

> 数据丢了能否从别处重新获得？  
> 能重建 → 当缓存用；  
> 不能重建 → 当内存数据库用。

| 维度   | 内存数据库       | 缓存        |
| ---- | ----------- | --------- |
| 数据地位 | 权威数据源       | 数据库副本     |
| 丢失后果 | 严重          | 可接受，可重建   |
| 持久化  | 必须开启        | 不强求       |
| TTL  | 一般不设置       | 普遍设置      |
| 内存淘汰 | 尽量避免        | 正常机制      |
| 一致性  | Redis 自身为标准 | 需与数据库保持同步 |
**作为内存数据库**
- 分布式 Session
- 计数器：访问量、点赞、库存扣减
- 实时排行榜（ZSET）
- 在线状态、心跳
- 分布式锁（SET NX PX / Redisson）
- 验证码、一次性令牌
- 限流与防刷
- 轻量消息队列（List / Stream）

**作为缓存**
- 热点数据：商品详情、用户资料、文章内容
- 复杂查询结果缓存
- 页面片段 / 接口结果缓存
- 共享配置缓存
- 秒杀、活动数据预热
- 用户信息等副本数据共享

![[Pasted image 20260822204043.png]]![[Pasted image 20260822204048.png]]
#### memcache(硬编码)
![[Pasted image 20260822204310.png]]
![[Pasted image 20260822204918.png]]![[Pasted image 20260822204930.png]]

#### jetcache(底层的cache)
![[Pasted image 20260822205119.png]]
![[Pasted image 20260822205152.png]]
![[Pasted image 20260822210933.png]]![[Pasted image 20260822210948.png]]
红色必须配置

![[Pasted image 20260822211023.png]]

注意local要设置keyConvertor,因为一般来说都是用string类型进行匹配. 注意toString方法一般是在测试中进行,我们要把它转换json就行了,采用的是阿里的技术.



![[Pasted image 20260822211354.png]]![[Pasted image 20260822211403.png]]![[Pasted image 20260822211417.png]]![[Pasted image 20260822211424.png]]
序列化流/对象操作输出流，将java对象写到本地文件中，注意这个类一定要实现serializable接口,只有实现了serializable接口，我们才能够将对象进行序列化

反序列化流/对象操作输入流，将java对象读入到程序中来。 

**一些小细节**，当我们实现了javabean类对象的序列化对象之后，如果后期业务有需求改变javabean类，那么此时反序列化进行匹配的时候，由于版本号发生了改变，因此此时java就会抛出异常。所以我们在实际过程中需要固定版本号

transient： 关键字，可以让我们想要的属性不会被序列化到文件中

![[Pasted image 20260802110459.png]]![[Pasted image 20260802114504.png]]



#### j2tcache
