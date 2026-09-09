# 基础篇
## 微服务技术对比
![[Pasted image 20260908104240.png]]
## 微服务远程调用
![[Pasted image 20260908105142.png]]![[Pasted image 20260908105416.png]]

## Eureka
### 提供者和消费者
![[Pasted image 20260908105613.png]]
### 注册中心
![[Pasted image 20260908105935.png]]
![[Pasted image 20260908110015.png]]![[Pasted image 20260908110034.png]]

### Eureka注册中心搭建
#### EurekServer搭建
![[Pasted image 20260908110505.png]]

#### EureKaClient服务注册

![[Pasted image 20260908110753.png]]
#### EureKaClient服务拉取
![[Pasted image 20260908111122.png]]

## Ribbon
### 负载均衡
#### 流程图
![[Pasted image 20260908111258.png]]![[Pasted image 20260908111721.png]]

#### IRule接口实现负载均衡
![[Pasted image 20260908111848.png]]![[Pasted image 20260908112119.png]]

第一个方式是全局的配置,对于order-service的所有负载均衡策略都是随机的, 但是对于配置文件而言是只针对访问userservice服务的

### 饥饿加载
![[Pasted image 20260908112615.png]]
‘
## Nacos
### 注册和发现, 略
### Nacos 服务分级存储模型
![[Pasted image 20260908113802.png]]

### NacosRule负载均衡
![[Pasted image 20260908114222.png]]
![[Pasted image 20260908114528.png]]、

### Nacos服务实例的权重设置
![[Pasted image 20260908114857.png]]

### Nacos环境隔离
![[Pasted image 20260908114957.png]]
![[Pasted image 20260908115127.png]]

### Nacos和Eureka之间的区别
![[Pasted image 20260908115619.png]]![[Pasted image 20260908115633.png]]![[Pasted image 20260908115828.png]]

**AP：优先保证可用性（Availability）**

**CP：优先保证一致性（Consistency）**

### Nacos 配置管理
#### 配置管理的流程
![[Pasted image 20260908144521.png]]
![[Pasted image 20260908144700.png]]
![[Pasted image 20260908144739.png]]


#### 配置的自动刷新
![[Pasted image 20260908145119.png]]
![[Pasted image 20260908145242.png]]

#### 多环境配置共享
![[Pasted image 20260908145615.png]]![[Pasted image 20260908150031.png]]

### Nacos集群搭建
稍微配置一下nacos 和 nginx 的文件就行了

## Feign
### 基础使用
![[Pasted image 20260908151203.png]]
![[Pasted image 20260908151219.png]]

![[Pasted image 20260908151142.png]]


### 自定义Feign的配置
![[Pasted image 20260908151334.png]]![[Pasted image 20260908151438.png]]
![[Pasted image 20260908151603.png]]

### 性能优化
 ![[Pasted image 20260908151819.png]]
 ![[Pasted image 20260908151906.png]]

### 最佳实践
![[Pasted image 20260908152233.png]]
![[Pasted image 20260908152427.png]]


## 统一网关Gateway

![[Pasted image 20260908160023.png]]

| 对比项             | Gateway     | Filter            | Interceptor          |
| --------------- | ----------- | ----------------- | -------------------- |
| 所属层次            | **微服务架构层**  | **Servlet/Web 层** | **Spring MVC 层**     |
| 作用范围            | 整个微服务系统     | 单个 Web 应用         | 单个 Spring MVC 应用     |
| 所在位置            | 微服务最外层      | Servlet 处理链       | DispatcherServlet 内部 |
| 主要作用            | 路由、统一鉴权、限流等 | Web 请求的通用处理       | Controller 请求的拦截     |
| 是否属于 Spring MVC | 否           | 否                 | **是**                |

Nginx 当然可以完成 Gateway 的路由功能，两者并不是能力上的绝对替代关系。Spring Cloud Gateway 的优势主要在于它天然面向 Spring Cloud 微服务体系，可以与 Nacos 等服务注册中心结合，根据动态的服务实例进行路由，并在此基础上实现微服务级别的鉴权、限流、熔断等治理。

|组件|主要面对谁|核心关注点|
|---|---|---|
|**Nginx**|外部客户端|**入口流量如何分发**|
|**Gateway**|微服务系统|**请求应该去哪个服务/实例**|
|**Ribbon / LoadBalancer**|服务调用方|**这次调用具体选哪个实例**|

![[Pasted image 20260908160117.png]]

### 快速入门
![[Pasted image 20260908160151.png]]![[Pasted image 20260908160650.png]]

![[Pasted image 20260908160931.png]]
### 路由断言工厂
![[Pasted image 20260908161056.png]]
![[Pasted image 20260908161113.png]]

### 路由过滤器
![[Pasted image 20260908161623.png]]
![[Pasted image 20260908161635.png]]![[Pasted image 20260908161746.png]]![[Pasted image 20260908161939.png]]

### 全局过滤器
![[Pasted image 20260908162104.png]]

### 过滤器的执行顺序
![[Pasted image 20260908162756.png]]
这里的globalfilter被一个adapter适配器转换成了GatewayFilter了

![[Pasted image 20260908163008.png]]

### 网关的跨域问题

> **CORS 是一种跨域资源共享机制，主要用于解决浏览器同源策略导致的跨域访问问题。服务器通过响应头声明允许哪些来源、请求方法和请求头访问资源，浏览器根据这些响应头决定是否允许前端读取响应。**

![[Pasted image 20260908165559.png]]

其实在这里来说, 我们之前的nginx就很好的解决了这些问题, 通过反向代理
![[Pasted image 20260908165650.png]]


# 高级篇

## Sentinel
### 雪崩问题
![[Pasted image 20260909144258.png]]
![[Pasted image 20260909144731.png]]

最主要的问题:由于当前请求卡在一个业务上, 导致后续的请求全部失效, 甚至会影响其他业务的情况.
所以我们的思路:
	1. 请求失败了, 我要及时的告诉业务你这个发生错误了, 直接返回不要阻塞
	2. 对于某一个业务来说,我让你的请求数量控制在一定的范围里面,不要影响这个服务的其他业务
	3. 如果出现了某一个阈值, 那么就直接把这个服务给熔断处理
	4. 实现做好预防, 防止流量带来的故障问题


![[Pasted image 20260909145241.png]]


### 基础概念
![[Pasted image 20260909150141.png]]

### 限流规则
![[Pasted image 20260909150306.png]]
![[Pasted image 20260909150413.png]]

![[Pasted image 20260909150442.png]]

#### 流控模式
##### 直接
![[Pasted image 20260909150800.png]]
##### 关联
![[Pasted image 20260909150852.png]]![[Pasted image 20260909151044.png]]

##### 链路
![[Pasted image 20260909151338.png]]
![[Pasted image 20260909151522.png]]

#### 流控效果
![[Pasted image 20260909151556.png]]

##### 快速失败
略
##### warm up
![[Pasted image 20260909151728.png]]

##### 排队等待
![[Pasted image 20260909151934.png]]

#### 热点参数限流
![[Pasted image 20260909152149.png]]
**注意: 这里的参数索引是指代你访问的controller 那一个方法里面的参数, 而不是指代的请求路径那一个id**
```
@GetMapping("/orders/{id}")
public Order getOrder(@PathVariable("id") Long id) {
    ...
}

代表的是第0个参数, 也就是id, 那么此时就是对多有的query访问都是限流的
```
![[Pasted image 20260909152232.png]]![[Pasted image 20260909152316.png]]


### 隔离和降级
![[Pasted image 20260909153355.png]]
#### Feign整合Sentinel
![[Pasted image 20260909153522.png]]
 ![[Pasted image 20260909153606.png]]
 ![[Pasted image 20260909153621.png]]

#### 线程隔离
![[Pasted image 20260909155106.png]]
![[Pasted image 20260909155248.png]]


这里的线程池隔离是通过创建新的线程来处理不同的业务,这就带来了好处, 如果我要处理这个业务的话, 那么我是一部执行的, 同时由于线程池能够获取当前的线程, 那么你就能够自定义的做各种处理; 但是由于采用线程池会带来很大的开销.

![[Pasted image 20260909155457.png]]


#### 熔断降级
![[Pasted image 20260909155854.png]]

#### 熔断策略-慢调用
![[Pasted image 20260909160011.png]]

#### 熔断策略-异常比例、异常数
![[Pasted image 20260909160247.png]]

### 授权规则
FallbackFactory 处理的是 Feign 调用失败后的“降级逻辑”；BlockExceptionHandler 处理的是 Sentinel 规则触发后的“流控/熔断异常”。

**注意默认是default值**, 这里要得到看请求头是否含有这个值, 因此我们需要重写这个方法来进行隔离
![[Pasted image 20260909160729.png]]![[Pasted image 20260909160743.png]]


|1|BlockExceptionHandler|FallbackFactory|
|---|---|---|
|属于谁|Sentinel|OpenFeign|
|处理什么|Sentinel 的 BlockException|Feign 调用失败|
|触发原因|流控、熔断、热点参数等 Sentinel 规则|服务不可用、网络异常、超时、500 等|
|位置|Sentinel 资源执行层|Feign 客户端调用层|
|关注点|Sentinel 为什么不让我执行|远程调用为什么失败|
|典型场景|QPS 超限|服务宕机/超时|
|作用|返回 Sentinel 降级结果|返回 Feign fallback 结果|
```
                         请求
                          │
                          ↓
                    Sentinel资源
                          │
              ┌───────────┴───────────┐
              │                       │
        Sentinel放行               Sentinel拦截
              │                       │
              ↓                       ↓
            Feign                BlockException
              │                       │
       ┌──────┴──────┐                ↓
       │             │       BlockExceptionHandler
    调用成功       调用失败
       │             │
       ↓             ↓
     正常返回    FallbackFactory
```

eign 是远程调用的发起方，FallbackFactory 本质上就是给这个“远程调用”准备兜底方案的；而 BlockExceptionHandler 是 Sentinel 资源层面的统一异常处理，两者不是一个维度的东西。