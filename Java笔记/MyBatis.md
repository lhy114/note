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

## 标准数据层CRUD功能《看文档就行了》
### CRUD
![[Pasted image 20260819114054.png]]
### 分页查询（既然你的分页查询是limit，那么MP实现的思路就是拦截到SQL语句，然后进行增强就行了，因此这里是定义的一个拦截器）
![[Pasted image 20260819115256.png]]![[Pasted image 20260819115314.png]]![[Pasted image 20260819115319.png]]

```
    @Override  
    public PageBean page(String name,  
                         Short gender,  
                         LocalDate begin,  
                         LocalDate end,  
                         Integer page,  
                         Integer pageSize) {  
//        Long total = empMapper.Count();  
//        List<Emp> emps = empMapper.page((page-1)*pageSize, pageSize);  
  
        //1. 设置页表        PageHelper.startPage(page,pageSize);  
  
        //2. 查询所有的数据  
        List<Emp> emplist = empMapper.list(name, gender, begin ,end);  
  
        //3. 将查询到的数据转换为PageHelp类型的数据  
        PageInfo<Emp> pageInfo = new PageInfo<>(emplist);  
  
        PageBean pageBean = new PageBean(pageInfo.getTotal(), pageInfo.getList());  
        return pageBean;  
    }
    
    
    
    public List<Emp> list(String name, Short gender, LocalDate begin, LocalDate end);
    
    
    <select id="list" resultType="com.cqu.tilas.pojo.Emp">  
    select * from emp  
    <where>  
        <if test="name!=null and name!=''">  
            name like concat('%', #{name}, '%')  
        </if>  
        <if test="gender != null">  
            and gender = #{gender}  
        </if>  
        <if test="begin!=null and end!=null">  
            and entrydate between #{begin} and #{end}  
        </if>  
    </where>  
    order by update_time desc  
</select>
```

两者都是解决 MyBatis 分页的方案，但实现机制和用法差别很大。核心区别：**PageHelper 靠 ThreadLocal 自动拦截下一条 SQL；MP 的分页靠显式传入 Page 对象和内置分页插件。**

**PageHelper**

- 独立的分页插件，不依赖 MyBatis-Plus，原生 MyBatis 也能用。
- 原理：`PageHelper.startPage(...)` 会把分页参数放进 **ThreadLocal**，再通过 MyBatis 拦截器对紧随其后的第一条查询 SQL 自动改写，拼上 `LIMIT`。
- 分页参数和查询是分开的，容易“串页”：线程复用、方法里多写一条查询时，可能作用于错误的 SQL。
- 返回值仍是普通的 `List`，总数通过 `PageInfo` 或强转 `Page` 拿到。

```
PageHelper.startPage(pageNum, pageSize);
List<User> users = userMapper.selectList(...);
PageInfo<User> pageInfo = new PageInfo<>(users);
```

**MyBatis-Plus 分页**

- 内置 `PaginationInnerInterceptor`，先注册到 MyBatis 的拦截器链里。
- 原理：分页参数封装在 `IPage`（常用 `Page<T>`）对象里，拦截器检测到 SQL 中带了分页对象后自动处理，并为不同数据库生成对应方言 SQL。
- 没有 ThreadLocal，参数随查询方法显式传递，更可控。
- 返回 `IPage`，`records` 是数据，`total` 是总数，`pages` 是总页数。

```
Page<User> page = new Page<>(pageNum, pageSize);
IPage<User> result = userMapper.selectPage(page, wrapper);

List<User> users = result.getRecords();
long total = result.getTotal();
```

**对比表格**

|维度|PageHelper|MyBatis-Plus 分页|
|---|---|---|
|定位|独立第三方插件|MP 内置能力|
|分页参数传递|ThreadLocal 隐式传递|`Page` 对象显式传入|
|使用前提|查询前必须 `startPage`|注册 `PaginationInnerInterceptor`|
|返回结果|`List`，总数靠 `PageInfo`|`IPage`，含总数和分页信息|
|线程安全性|依赖 ThreadLocal，需注意清理/顺序|无共享状态，更安全|
|数据库方言|需配置 `dialect` 或自动识别|内置常见方言|
|与 MP 通用方法|能配合但不推荐|原生支持 `selectPage`|
|适合场景|已有原生 MyBatis 项目|已使用 MyBatis-Plus 的项目|

**实践建议**

- 项目里已经用 MyBatis-Plus，就用它的 `selectPage` + `PaginationInnerInterceptor`，不要两个分页插件同时启用，否则可能重复拦截、SQL 被拼两次。
- 老项目是原生 MyBatis 且不想引入 MP，用 PageHelper 更合适。
- 如果使用 MP，但 PageHelper 拦截器比 MP 的分页拦截器先执行，也可能把 MP 的 SQL 再包一层，出现分页异常，所以二选一即可。

### 条件查询
![[Pasted image 20260819120222.png]]![[Pasted image 20260819121053.png]]![[Pasted image 20260819121101.png]]![[Pasted image 20260819121127.png]]

### null的处理
![[Pasted image 20260819121628.png]]

### 查询投影

![[Pasted image 20260819141810.png]]![[Pasted image 20260819142204.png]]![[Pasted image 20260819142210.png]]

### 字段映射和表明映射
![[Pasted image 20260819142358.png]]