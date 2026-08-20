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
**文件的优先级为：** properties > yml > yaml. 不同配置文件中相同配置按照加载优先级相互覆盖，不同配置文件中不同配置全部保留
![[Pasted image 20260820113327.png]]
