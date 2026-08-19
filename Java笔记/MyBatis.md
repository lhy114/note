## 定义
![[Pasted image 20260812162705.png]]![[Pasted image 20260812164813.png]]

```
@Mapper //在运行的时候，会自动生成这个接口的实现类对象（通过代理的方式），然后将这个对象交给IOC容器进行处理  
public interface UserMapper {  
  
    @Select("SELECT * from user")  
    public List<User> list();  
}
```

![[Pasted image 20260812172931.png]]
## 基础用法

![[Pasted image 20260812201326.png]]

![[Pasted image 20260812201623.png]]![[Pasted image 20260812202218.png]]![[Pasted image 20260812202319.png]]![[Pasted image 20260812202534.png]]![[Pasted image 20260812202619.png]]![[Pasted image 20260812202918.png]]![[Pasted image 20260812203129.png]]

## XML文件
![[Pasted image 20260814102448.png]]

## 动态SQL
![[Pasted image 20260814104103.png]]
### if
![[Pasted image 20260814110414.png]]
![[Pasted image 20260814112447.png]]

### foreach
![[Pasted image 20260814113400.png]]
## sql&include
![[Pasted image 20260814113812.png]]


# MyBatis Plus
## 入门
![[Pasted image 20260819113501.png]]![[Pasted image 20260819113603.png]]![[Pasted image 20260819113622.png]]![[Pasted image 20260819113643.png]]

## 介绍
两者的核心区别一句话概括：**MyBatis-Plus 是在 MyBatis 之上做的增强封装，并没有替换 MyBatis，而是“用起来更方便”的脚手架。**

**MyBatis**

- 轻量级持久层框架，SQL 和映射由你手动写在 Mapper XML 或注解里。
- 没有内置通用 CRUD，增删改查都要自己写 SQL 或接口方法。
- 分页、批量操作、逻辑删除等需要自己实现或引入第三方插件。
- 灵活，SQL 完全可控，但重复代码多。

**MyBatis-Plus**

- 基于 MyBatis，运行时底层仍然是 MyBatis，只是自动生成了很多常用能力。
- 提供 `BaseMapper<T>`，不写 SQL 就能得到 `selectById`、`insert`、`updateById`、`deleteById`、`selectList` 等通用方法。
- 提供 `Wrapper`（如 `LambdaQueryWrapper`）做条件查询，用 Java 链式写法替代手写动态 SQL。
- 内置分页插件 `PaginationInnerInterceptor`，`selectPage` 直接分页。
- 内置逻辑删除、乐观锁、自动填充（创建时间、更新时间）、代码生成器等能力。
- 对复杂 SQL 仍然支持：照常写自定义 Mapper 方法或 XML，MP 不限制。

**一句话对比**

|维度|MyBatis|MyBatis-Plus|
|---|---|---|
|定位|持久层框架|MyBatis 增强工具|
|通用 CRUD|需手写|内置|
|条件查询|手写 SQL/动态 SQL|`Wrapper` 链式|
|分页|需自己实现|插件一键分页|
|逻辑删除/乐观锁|需自己实现|内置支持|
|复杂自定义 SQL|完全可控|同样支持|
|适用场景|需要极致 SQL 控制|追求开发效率、CRUD 为主的业务|

## 标准数据层CRUD功能
![[Pasted image 20260819114054.png]]