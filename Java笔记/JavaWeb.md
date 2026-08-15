# SpringBootWeb
![[Pasted image 20260812103451.png]]
Controller、Service、Mapper 都放在启动类所在包的下面。

# HTTP协议
![[Pasted image 20260812103818.png]]
无状态：没有请求响应是没有记忆的，也就是说一次请求就要响应一次，不同请求之间是没有关联的。

## 请求协议
![[Pasted image 20260812104311.png]]
![[Pasted image 20260812104228.png]]
## 响应协议
![[Pasted image 20260812105021.png]]![[Pasted image 20260812105038.png]]

## 协议解析和web服务器
![[Pasted image 20260812110252.png]]
![[Pasted image 20260812110418.png]]
**Tomcat 是什么**
Tomcat 是一个用 Java 写的 **Web 服务器 / Servlet 容器**，由 Apache 基金会开发。它的核心能力是：

- 监听一个端口（默认 8080）
- 接收浏览器发来的 HTTP 请求
- 把请求交给 Java 程序处理
- 再把 Java 程序返回的结果包装成 HTTP 响应发给浏览器

它实现的是 Servlet 规范，也就是说它知道怎么调用 `Servlet`，而 Spring MVC 里的 `DispatcherServlet` 本质上就是一个 Servlet。

**spring**本身不是服务器，不负责监听端口、接收请求。Spring Web MVC 负责的是“收到请求后，调用哪个 Controller 方法，返回什么数据”这套业务逻辑。

浏览器 → HTTP 请求 → Tomcat → Spring 的 DispatcherServlet → Controller → 返回结果 → Tomcat → 浏览器.**注意，现在的spring已经集成了tomcat**
![[Pasted image 20260812111344.png]]

![[Pasted image 20260812110806.png]]

# 请求响应
![[Pasted image 20260812114256.png]]![[Pasted image 20260812114327.png]]
## 请求
![[Pasted image 20260812150030.png]]
![[Pasted image 20260812124409.png]]
```
package com.cqu.controller;  
  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RequestParam;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
public class simpleRequest {  
    @RequestMapping("/getParam")  
    public String getParam(String name, @RequestParam(name = "age", required = true) Integer value) {  
        System.out.println(name+","+value);  
        System.out.println();  
        return "ok";  
    }  
}
```

![[Pasted image 20260812125800.png]]![[Pasted image 20260812142900.png]]![[Pasted image 20260812143019.png]]![[Pasted image 20260812143618.png]]![[Pasted image 20260812144446.png]]

| 写法     | 含义              |
| ------ | --------------- |
| `yyyy` | 年               |
| `MM`   | 月，**必须大写**      |
| `dd`   | 日               |
| `HH`   | 24 小时制，**必须大写** |
| `mm`   | 分钟，小写           |
| `ss`   | 秒               |
![[Pasted image 20260812145026.png]]![[Pasted image 20260812145615.png]]

## 响应，这里要学习统一响应结果
![[Pasted image 20260812150147.png]]![[Pasted image 20260812150602.png]]
![[Pasted image 20260812150802.png]]

# 分层解耦
## 三层架构（目的是希望每一个 类是单一职责的）
![[Pasted image 20260812151833.png]]
![[Pasted image 20260812160546.png]]
## 控制反转和依赖注入
![[Pasted image 20260812160822.png]]
![[Pasted image 20260812161155.png]]

### IOC 控制反转
![[Pasted image 20260812161540.png]]
![[Pasted image 20260812161824.png]]
### DI 依赖注入
![[Pasted image 20260812162354.png]]
![[Pasted image 20260812162251.png]]

# Restful
![[Pasted image 20260814121833.png]]

# PageHelp
![[Pasted image 20260814170043.png]]

# 文件上传
![[Pasted image 20260814201453.png]]
![[Pasted image 20260814202252.png]]
这里要明白前端上传的三要素，post，enctype和type，注意如果使用enctype为默认值xx-www-xx，那么此时只能够上传文件名而不会上传文件
![[Pasted image 20260814201711.png]]
## 本地存储
![[Pasted image 20260814202607.png]]
这里要回顾一下UUID生成唯一的id值
![[Pasted image 20260814202736.png]]![[Pasted image 20260814203124.png]]
## OSS(以阿里云为案例)
![[Pasted image 20260814205403.png]]

# 参数配置化
![[Pasted image 20260814215115.png]]![[Pasted image 20260814215307.png]]![[Pasted image 20260814215509.png]]![[Pasted image 20260814215607.png]]![[Pasted image 20260814220111.png]]
![[Pasted image 20260814220740.png]]
利用IOC进行自动注入值，首先你要给IOC容器，所以需要Component注解，然后需要调用get、set方法，所以需要data注解，然后需要识别前缀，所以需要ConfigurationProperties

# 登陆的认证和授权
![[Pasted image 20260815100446.png]]

## 会话技术
![[Pasted image 20260815100916.png]]
由于http协议是无状态，每一次请求响应之间是没有关系的，但是为了能够实现会话跟踪技术，需要让每一次请求都含有共有数据。

### Cookis和Session
![[Pasted image 20260815101849.png]]![[Pasted image 20260815102353.png]]

| 对比项  | Cookie                   | Session                   |
| ---- | ------------------------ | ------------------------- |
| 存储位置 | 浏览器客户端                   | 服务器端                      |
| 存储大小 | 单个约 4KB，数量有限             | 取决于服务器配置，一般远大于 Cookie     |
| 生命周期 | 可设置过期时间，持久保存             | 默认浏览器关闭或超时后失效，也可设置有效期     |
| 安全性  | 明文存储在客户端，容易被篡改和窃取        | 数据在服务器，客户端只能看到 Session ID |
| 性能   | 每次请求都随 HTTP 头传输，会增加流量    | 占用服务器内存，用户多时增加服务器压力       |
| 用途   | 适合非敏感信息，如语言偏好、主题、购物车简单数据 | 适合敏感信息，如登录状态、用户权限、购物车完整数据 |

## JWT技术
![[Pasted image 20260815102630.png]]
![[Pasted image 20260815103021.png]]
JWT 的 payload（以及 header）用的是 **Base64URL 编码**
```
客户端                   服务器
  │  1. 用户名 + 密码       │
  ├───────────────────────► │ 验证密码
  │                        │ 用【服务器自己的私钥】对 header.payload 签名
  │  2. 返回 JWT           │
  │ ◄──────────────────────┤
  │  3. 之后每次请求带 JWT  │
  ├───────────────────────► │ 用【服务器自己的公钥】验签
  │                        │ 验签通过 → 信任 payload 里的 userId
  
  HS256注意是的对称密钥
  
  JWT 承担的是“身份凭证 + 授权信息”，不是保密，也不是防冒充；通道安全靠 TLS，防窃取靠短时效和存储策略，可撤销靠服务端黑名单/刷新令牌，防冒充靠 token 绑定和设备风控。单靠任何一层都不够，安全本来就是分层叠加的结果。
  
```


![[Pasted image 20260815103130.png]]![[Pasted image 20260815103456.png]]

返回得到jwt令牌存储到本地，然后通过前端自动获得，然后进行登陆校验

## 拦截
### Filter
![[Pasted image 20260815111153.png]]
![[Pasted image 20260815111422.png]]
![[Pasted image 20260815114222.png]]
![[Pasted image 20260815114409.png]]
![[Pasted image 20260815114644.png]]

### Interceptor
![[Pasted image 20260815141752.png]]![[Pasted image 20260815141947.png]]![[Pasted image 20260815142432.png]]![[Pasted image 20260815142836.png]]

这里我要稍微说明一下，由于filter它不属于spring框架下面的内容，因此TomCat是无法直接访问controller的，所以需要这个dispathcerservlet进行操作。 
```
浏览器请求
    │
    ▼
DispatcherServlet  (唯一入口)
    │
    ▼
HandlerMapping    (URL → Controller 方法 + 拦截器)
    │
    ▼
HandlerAdapter    (调用具体 Controller 方法)
    │
    ▼
Controller        (业务方法，返回 ModelAndView 或 @ResponseBody 数据)
    │
    ▼
ViewResolver      (视图名 → 真正的视图)
    │
    ▼
View              (渲染成 HTML / JSON)
    │
    ▼
响应给浏览器
```

# 全局异常处理器
![[Pasted image 20260815160614.png]]

# Spring的事物管理
![[Pasted image 20260815161216.png]]![[Pasted image 20260815161708.png]]![[Pasted image 20260815161856.png]]

这里的required，是两个方法公用一个事物，如果a功能发生异常，即使之前运行了b方法，那么此时事物回滚，b的操作相当于没有执行。对于那些出现错误，需要记录日志的方法，这时候是无法记录的（假设b是一个log记录的方法）
![[Pasted image 20260815162429.png]]

## AOP，面向切面编程（感觉不如面向方法编程）
![[Pasted image 20260815162907.png]]
![[Pasted image 20260815163504.png]]

动态代理，简单说就是：**在程序运行时，动态地生成一个代理对象，让它替真实对象干活，并在干活前后插入额外逻辑**。它的关键在“动态”：代理类不是程序员手写的，而是 JVM 运行时生成出来的。

先看静态代理，理解“代理”是什么

```
// 接口
public interface UserService {
    void login(String username);
}

// 真实对象
public class UserServiceImpl implements UserService {
    @Override
    public void login(String username) {
        System.out.println("用户登录：" + username);
    }
}

// 静态代理：手动写一个代理类
public class UserServiceProxy implements UserService {
    private UserService target;

    public UserServiceProxy(UserService target) {
        this.target = target;
    }

    @Override
    public void login(String username) {
        System.out.println("登录前：记录日志");
        target.login(username);
        System.out.println("登录后：记录日志");
    }
}
```

静态代理的缺点很明显：**每个接口都要手写一个代理类**，方法一多就全是重复代码。

 JDK 动态代理

JDK 动态代理只需要一个 `InvocationHandler`，一个处理器可以代理所有接口：

```
public class LogHandler implements InvocationHandler {

    private final Object target;

    public LogHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("方法执行前：" + method.getName());

        Object result = method.invoke(target, args);

        System.out.println("方法执行后：" + method.getName());
        return result;
    }
}
```

使用：

```
UserService target = new UserServiceImpl();

UserService proxy = (UserService) Proxy.newProxyInstance(
        target.getClass().getClassLoader(),
        target.getClass().getInterfaces(),
        new LogHandler(target)
);

proxy.login("admin");
```

此时 `proxy` 是 JVM **在运行时自动生成**的一个类，它实现了 `UserService` 接口，方法调用都会先进入 `LogHandler.invoke()`。核心要求：**目标类必须实现接口**，因为它基于接口生成代理。

 CGLIB 动态代理

CGLIB 不要求接口，它直接**生成目标类的子类**，在子类里重写方法：

```
Enhancer enhancer = new Enhancer();
enhancer.setSuperclass(UserServiceImpl.class);
enhancer.setCallback((MethodInterceptor) (obj, method, args, proxy) -> {
    System.out.println("方法执行前");
    Object result = proxy.invokeSuper(obj, args);
    System.out.println("方法执行后");
    return result;
});

UserServiceImpl proxy = (UserServiceImpl) enhancer.create();
proxy.login("admin");
```

限制：目标类**不能被 final 修饰**，方法也不能是 final 的，因为要生成子类重写。

两者对比

|对比项|JDK 动态代理|CGLIB|
|---|---|---|
|原理|基于接口，运行时生成实现类|基于继承，生成目标类的子类|
|目标类要求|必须实现接口|类不能 final|
|性能（老版本）|反射调用较慢|生成字节码，性能更好|
|Spring 中场景|有接口时|没有接口或强制 class 代理|

Spring AOP 的代理，**有接口时历史上用 JDK 代理，没有接口用 CGLIB**；现在 Spring Boot 2+ 默认 `proxyTargetClass = true`，也就是默认走 CGLIB 类代理。

常见应用场景

- **Spring AOP**：事务、日志、权限、性能监控，都是通过动态代理拦截方法
- **MyBatis Mapper**：你只写了接口，MyBatis 用 JDK 动态代理生成实现，帮你执行 SQL
- **Feign**：声明式 HTTP 客户端，接口被动态代理后变成真实 HTTP 调用
- **Spring `@Transactional`**：事务的开启、提交、回滚，全靠代理在方法前后插入逻辑
- **远程调用、缓存、重试**：都是“在不改业务代码的情况下，给方法加一层能力”

和反射的关系

动态代理底层经常用到反射：`method.invoke(target, args)`。理解顺序是：

```
静态代理：手写代理类
动态代理：运行时生成代理类
           ├── JDK：基于接口 + InvocationHandler
           └── CGLIB：基于继承 + MethodInterceptor
```

**一句话总结：动态代理就是运行时生成的“替身”，在不动原类代码的前提下，把日志、事务、权限这些公共逻辑统一插到方法前后，Spring AOP、MyBatis、Feign 这些框架全靠它。**

![[Pasted image 20260815164533.png]]![[Pasted image 20260815164627.png]]


### 通知类型
![[Pasted image 20260815165053.png]]![[Pasted image 20260815165539.png]]![[Pasted image 20260815165818.png]]
### 通知顺序
![[Pasted image 20260815170049.png]]

### 切入点表达式
![[Pasted image 20260815170217.png]]![[Pasted image 20260815170259.png]]![[Pasted image 20260815170558.png]]![[Pasted image 20260815170737.png]]![[Pasted image 20260815170803.png]]![[Pasted image 20260815171015.png]]
![[Pasted image 20260815171520.png]]

#### 一、注解本质是什么

注解就是一段**元数据**，它本身不写逻辑、不执行代码，只是“贴在”类、方法、字段上，供框架或反射代码读取。

```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface OperationLog {
    String value();
}
```

这就是一个最简单的自定义注解。注意几个语法特征：

- 用 `@interface` 声明，不是 `interface`
- 里面的 `value()` 不是方法，是**注解元素**，相当于一个参数
- 注解元素不能有 `throws`，不能用泛型
- 使用时的写法是 `@OperationLog("登录")`，而不是 OperationLog("登录" = ...)，省略了 `value`

#### 二、注解元素规则

元素类型只允许这些：

- 基本类型：`int`、`long`、`boolean` 等
- `String`
- `Class` 及其泛型：`Class<?>`
- 枚举：`enum`
- 注解类型
- 以上类型的数组

允许写法：

```
public @interface MyAnnotation {
    String value() default "";              // String
    int level() default 1;                  // 基本类型
    Class<?> target() default Object.class; // Class
    LevelEnum levelEnum() default LevelEnum.INFO; // 枚举
    String[] tags() default {};             // 数组
}
```

规则：

- 元素可以给默认值 `default`
- 使用时，没有默认值的元素**必须填**
- 默认值不能是 `null`
- 只有一个元素且叫 `value` 时，使用时可以省略名字：`@MyAnnotation("x")`；多个元素时写成 `@MyAnnotation(value = "x", level = 2)`

#### 三、元注解：注解的注解

定义自定义注解时，通常要加几个 JDK 自带的元注解。

**@Target：指定能贴在哪里**

|值|位置|
|---|---|
|`TYPE`|类、接口、枚举|
|`METHOD`|方法|
|`FIELD`|字段|
|`PARAMETER`|方法参数|
|`CONSTRUCTOR`|构造器|
|`ANNOTATION_TYPE`|注解类型本身|

```
@Target({ElementType.METHOD, ElementType.TYPE})
```

**@Retention：指定注解能活多久**

|值|生命周期|
|---|---|
|`SOURCE`|只在源码里，编译后丢弃，如 `@Override`|
|`CLASS`|存在字节码里，但运行时读不到，默认值|
|`RUNTIME`|运行时保留，**反射能读到，AOP 必须用它**|

```
@Retention(RetentionPolicy.RUNTIME)
```

**其他元注解**

```
@Documented   // 注解信息会被 javadoc 记录
@Inherited    // 子类继承父类的这个注解（仅限类上）
@Repeatable   // 允许同一个位置贴多个，如 @RequestMapping 的衍生
```

```
@Repeatable(Schedules.class)
public @interface Schedule {
    String time();
}

@Schedule(time = "9:00")
@Schedule(time = "18:00")
public void run() {}
```

#### 四、使用自定义注解

```
public class UserService {

    @OperationLog("查询用户")
    public User getUser(Long id) {
        return new User(id);
    }
}
```

到这一步，注解**什么都没干**，它只是挂在方法上。真正干活的是后面读取它的代码。

#### 五、通过反射读取注解（核心）

注解 + 反射才等于实际功能。常用 API：

```
boolean present = method.isAnnotationPresent(OperationLog.class);
OperationLog log = method.getAnnotation(OperationLog.class);
Annotation[] all = method.getAnnotations(); // 拿到方法上所有注解
```

完整解析例子：

```
import java.lang.reflect.Method;

public class AnnotationParser {

    public static void main(String[] args) throws Exception {
        Method method = UserService.class.getMethod("getUser", Long.class);

        // 1. 判断方法上有没有这个注解
        if (method.isAnnotationPresent(OperationLog.class)) {

            // 2. 取出注解对象
            OperationLog annotation = method.getAnnotation(OperationLog.class);

            // 3. 读取注解里的值
            System.out.println("操作类型：" + annotation.value());
        }
    }
}
```

注解对象是 JVM 在运行时根据 `@Retention(RUNTIME)` 生成的实例，读取方式类似于“找到标签，看标签上写的字”。

类、字段、参数同理：

```
// 类上的注解
Class<?> clazz = UserService.class;
OperationLog classLog = clazz.getAnnotation(OperationLog.class);

// 字段上的注解
Field field = clazz.getDeclaredField("username");
FieldLog fieldLog = field.getAnnotation(FieldLog.class);

// 参数上的注解
Parameter[] parameters = method.getParameters();
ParameterLog paramLog = parameters[0].getAnnotation(ParameterLog.class);
```

#### 六、一个综合例子

```
// 1. 定义注解
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RateLimit {
    int limit() default 10;      // 每秒上限
    String key() default "";     // 限流维度
}

// 2. 使用注解
public class OrderService {

    @RateLimit(limit = 5, key = "createOrder")
    public void createOrder() {
        System.out.println("创建订单");
    }
}

// 3. 反射解析
public class RateLimitParser {

    public static void check(Object obj, String methodName, Class<?>... paramTypes) throws Exception {
        Method method = obj.getClass().getMethod(methodName, paramTypes);
        RateLimit rateLimit = method.getAnnotation(RateLimit.class);

        if (rateLimit != null) {
            System.out.println("限流配置：key=" + rateLimit.key()
                    + ", limit=" + rateLimit.limit());
        }
    }
}
```

实际框架里，解析逻辑通常放在 AOP 切面或框架启动扫描阶段，而不是自己手写反射，但原理完全相同。

#### 七、常见坑

- **忘了加 `@Retention(RUNTIME)`**：反射永远拿到 `null`，这是最常见的错
- **`@Target` 写错位置**：比如注解只允许 `TYPE`，贴在方法上直接编译报错
- **元素默认值不能为 null**：想让“不填”和“填了默认”区分开，用特殊值或 `Optional` 思路解决
- **注解没有多态和继承**：注解的“继承”只有 `@Inherited` 管类上的注解，且子类没有自己的注解时才继承
- **`@Inherited` 对方法、字段不生效**：它只作用于类
- **注解元素是固定值**：不能是动态计算的结果，编译器要求在编译期确定



### 连接点
![[Pasted image 20260815171724.png]]