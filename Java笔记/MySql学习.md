
### 关系型数据库（RDBMS）
在关系模型基础上，由多张**二维表**构成的。

### 基础篇（基本语法的学习）
![[Pasted image 20260805150321.png]]

#### DDL
**数据库操作**
![[Pasted image 20260805150517.png]]
**二维表操作**
![[Pasted image 20260805151120.png]]
![[Pasted image 20260805151249.png]]
![[Pasted image 20260805152645.png]]![[Pasted image 20260805152740.png]]![[Pasted image 20260805152836.png]]![[Pasted image 20260805152951.png]]![[Pasted image 20260805153001.png]]![[Pasted image 20260805153142.png]]
#### DML
![[Pasted image 20260805163429.png]]
**添加数据**![[Pasted image 20260805160702.png]]
**修改数据**![[Pasted image 20260805162653.png]]
**删除数据**
![[Pasted image 20260805163237.png]]

####  SQL
##### SELECT关键字
![[Pasted image 20260805163707.png]]![[Pasted image 20260805163828.png]]![[Pasted image 20260805164254.png]]![[Pasted image 20260805165805.png]]![[Pasted image 20260805170126.png]]![[Pasted image 20260805170613.png]]![[Pasted image 20260805170920.png]]![[Pasted image 20260805172136.png]]
#### DCL 数据控制语音
![[Pasted image 20260805172517.png]]![[Pasted image 20260805203540.png]]![[Pasted image 20260805203635.png]]
#### 常见函数
![[Pasted image 20260805204150.png]]![[Pasted image 20260805204744.png]]![[Pasted image 20260805205039.png]]![[Pasted image 20260805205331.png]]
#### 约束
**常见约束**
![[Pasted image 20260806092136.png]]
**外键约束**
![[Pasted image 20260806093128.png]]![[Pasted image 20260806093546.png]]
前两个为默认行为。

#### 多表关系
##### 一对多
![[Pasted image 20260806094231.png]]
##### 多对多
![[Pasted image 20260806094307.png]]
##### 一对一
![[Pasted image 20260806094547.png]]
#### 多表查询
最主要的就是要明确你是内连接还是外连接，如果是内连接，你就用where，如果是外连接，老老实实用left outer join or right outer join

![[Pasted image 20260806095040.png]]
##### 内连接查询
![[Pasted image 20260806095137.png]]
##### 外连接查询
![[Pasted image 20260806095608.png]]![[Pasted image 20260806095916.png]]
##### 联合查询
![[Pasted image 20260806100646.png]]
#### 子查询
![[Pasted image 20260806100758.png]]
##### 标量子查询
![[Pasted image 20260806101102.png]]
##### 列子查询
![[Pasted image 20260806101157.png]]
##### 行子查询
![[Pasted image 20260806101650.png]]
#### 表子查询， 一般用作from语句后作为一个临时表
![[Pasted image 20260806101916.png]]

### 事务及其四大特性
![[Pasted image 20260806110302.png]]![[Pasted image 20260806110952.png]]![[Pasted image 20260806111010.png]]![[Pasted image 20260806111210.png]]
### 并发事务

![[Pasted image 20260806111540.png]]
![[Pasted image 20260806111626.png]]![[Pasted image 20260806111742.png]]