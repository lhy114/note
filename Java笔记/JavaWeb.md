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

# 参数p