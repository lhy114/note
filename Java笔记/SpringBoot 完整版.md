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
