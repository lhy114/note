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
1. 配置一个set方法，spring他会自动调用
![[Pasted image 20260817095707.png]]
2. 配置文件的实现DI
![[Pasted image 20260817095747.png]]

### Bean的配置
#### bean的基础配置
![[Pasted image 20260817100316.png]]
#### bean的别名
![[Pasted image 20260817100109.png]]

#### Bean的作用范围（这里指代的是单例还是多例）
![[Pasted image 20260817100236.png]]

### bean实例化

#### 通过无参构造方法访问
**注意这里的public改为private也能够实现**，这里通过反射的方法实现的
![[Pasted image 20260817100602.png]]

#### 静态工厂
![[Pasted image 20260817101109.png]]

#### 实例工厂
![[Pasted image 20260817101249.png]]

#### 使用FactoryBean实现实例工厂
![[Pasted image 20260817101553.png]]![[Pasted image 20260817101603.png]]

### Bean的生命周期
![[Pasted image 20260817102218.png]]

![[Pasted image 20260817102145.png]]![[Pasted image 20260817102152.png]]![[Pasted image 20260817102232.png]]

### 依赖注入的方式
![[Pasted image 20260817103344.png]]

#### setter-引用类型
![[Pasted image 20260817103705.png]]
#### setter-简单类型
![[Pasted image 20260817103728.png]]

#### 构造器-引用类型
![[Pasted image 20260817104245.png]]

#### 构造器-引用类型
![[Pasted image 20260817104303.png]]

#### DI方式的选择
![[Pasted image 20260817104426.png]]

### 自动装配
![[Pasted image 20260817105832.png]]![[Pasted image 20260817105915.png]]

#### 集合注入
