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
![[Pasted image 20260817112340.png]]
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
![[Pasted image 20260817112359.png]]
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

#### 集合注入，简单输入一遍就会了
![[Pasted image 20260817110537.png]]


### properties
![[Pasted image 20260817111455.png]]![[Pasted image 20260817111707.png]]

### 容器
#### 创建容器
![[Pasted image 20260817111933.png]]
#### 获得bean
![[Pasted image 20260817111946.png]]![[Pasted image 20260817112233.png]]![[Pasted image 20260817112244.png]]


### 注解开发（重点）
![[Pasted image 20260817140745.png]]
#### 基本注解
![[Pasted image 20260817112705.png]]
![[Pasted image 20260817112751.png]]![[Pasted image 20260817113030.png]]
#### Bean的生命周期
![[Pasted image 20260817133813.png]]

#### 自动装配-引用
![[Pasted image 20260817134128.png]]![[Pasted image 20260817134142.png]]

#### 自动装配-简单类型
![[Pasted image 20260817134542.png]]
#### 加载properties文件
![[Pasted image 20260817134459.png]]

#### 管理第三方bean
方法一：
![[Pasted image 20260817135001.png]]

方法二：
![[Pasted image 20260817135047.png]]![[Pasted image 20260817135102.png]]![[Pasted image 20260817135117.png]]



### Spring 整合 mybatis
1. 倒入pom-mybatis的坐标
2. 写对应参数，包括jdbc、mybatis的配置
![[Pasted image 20260817142108.png]]

3. mybatis实现
![[Pasted image 20260817142213.png]]![[Pasted image 20260817142359.png]]

![[Pasted image 20260817142407.png]]

这里也就是说spring mybatis其实创建了两个bean，一个创建一个mybatis对象，然后扫描对应的mapper。


原始的mybatis：**这里我们可以看到最关键的就是sqlsessionFatory这个类**，所以我们希望将这个对象整合到bean中
![[Pasted image 20260817142534.png]]
![[Pasted image 20260817142553.png]]![[Pasted image 20260817142609.png]]

## Spring 整合 junit（测试类）
![[Pasted image 20260817143145.png]]

## AOP，知道概念就行了，这里的spring写的很乱
![[Pasted image 20260817143413.png]]![[Pasted image 20260817143602.png]]![[Pasted image 20260817143800.png]]![[Pasted image 20260817145547.png]]![[Pasted image 20260817145124.png]]

### 切入点表达式
![[Pasted image 20260817145302.png]]![[Pasted image 20260817145327.png]]![[Pasted image 20260817145446.png]]
### 通知类型
![[Pasted image 20260817150309.png]]
![[Pasted image 20260817150202.png]]![[Pasted image 20260817150240.png]]

### 获得切点参数
![[Pasted image 20260817150659.png]]![[Pasted image 20260817151221.png]]![[Pasted image 20260817151235.png]]

## Spring的transcation
1. 步骤一![[Pasted image 20260817152157.png]]
2. 设置事物管理器，事务最终要由数据库存储引擎来执行，Java 层的 `@Transactional` 只是发指令，数据库不支持就白搭。![[Pasted image 20260817152240.png]]
3. 开启事物注解驱动
![[Pasted image 20260817152422.png]]

### Spring事务角色
![[Pasted image 20260817152617.png]]
![[Pasted image 20260817152645.png]]
默认值，只有一个事务，这样就做到同时成功，同时失败
![[Pasted image 20260817152714.png]]

### 事务的相关配置（默认只有出现了runtime exception才会回滚）
![[Pasted image 20260815161708.png]]

![[Pasted image 20260817152823.png]]
### 事务的传播行为
![[Pasted image 20260817153457.png]]![[Pasted image 20260817153553.png]]![[Pasted image 20260817153627.png]]