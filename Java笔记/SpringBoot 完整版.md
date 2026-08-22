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
你在测试的时候,写入到数据库里面的数据肯定不能留存,所以要进行回滚
![[Pasted image 20260822102503.png]]![[Pasted image 20260815161216.png]]