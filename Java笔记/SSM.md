# Spring Framework
![[Pasted image 20260817094235.png]]

## Core Container
### 核心概念
![[Pasted image 20260817094721.png]]
换句话来说，IOC容器自动创建bean对象，DI实现不同bean之间的依赖

### IoC
#### 通过配置文件的方法获得Bean和IoC容器
1. 创建Spring的配置文件
![[Pasted image 20260817095236.png]]
2. 初始化IoC容器，然后通过容器获得bean，你只需要记住一点，ApplicationContext是IoC容器就行了。
![[Pasted image 20260817095352.png]]
### DI
