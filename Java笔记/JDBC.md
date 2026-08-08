![[Pasted image 20260808191459.png]]![[Pasted image 20260808191619.png]]本机 `mysql-connector-j-9.6.0.jar` 里的实际内容
```
META-INF/services/java.sql.Driver 的内容：
com.mysql.cj.jdbc.Driver
```

而且 `javap` 反编译 `com.mysql.cj.jdbc.Driver` 也能看到它自己的静态代码块：

```
static {
    new Driver();
    DriverManager.registerDriver(new Driver());
}
```

所以整条链路是这样的：**首先加载驱动到JVM里面，然后驱动管理进行注册，也就是调用DriverManager.registerDriver**

**老写法里 `Class.forName` 到底在干什么**
```
Class.forName("com.mysql.jdbc.Driver");
Connection conn = DriverManager.getConnection(url, user, pwd);
```
`Class.forName("...")` 的作用不是“下载 jar”，也不是“把 jar 文件拿进内存”，而是：让 JVM 根据类名找到 `.class` 字节码，把它加载到 JVM 内存中，并且**执行这个类的静态代码块**。

MySQL 驱动的 `Driver` 类里有一个静态代码块，内容大致是：
```
static {
    DriverManager.registerDriver(new Driver());
}
```

也就是说，**老版本缺乏一个将驱动加载到JVM里面**“，注册驱动”的本质动作是 `DriverManager.registerDriver(...)`，而 `Class.forName` 只是想办法触发这段静态代码执行。因为你的 Java 代码里从头到尾没有 `new Driver()`，也没写过 `Driver` 类型，编译器根本不会主动去加载它；`Class.forName` 通过“反射”的方式，只用一个字符串类名就强制把类加载并初始化了，所以才会用反射。

**为什么现在不用写了**
JDK 6 开始，JDBC 4.0 引入了自动注册机制：驱动 jar 里只要包含一个文件
```
META-INF/services/java.sql.Driver
```
并在里面写上驱动类全名，`DriverManager.getConnection(...)` 第一次被调用时，就会自动用 `ServiceLoader` 扫描 classpath 里所有 jar 的这个文件，把列出来的驱动类加载并注册。

你本机这个 jar 里就有这个文件，所以：
```
Connection conn = DriverManager.getConnection(url, user, pwd);
```
这一句执行时，`DriverManager` 内部已经自动帮你做了“加载类 + 执行静态代码块 + registerDriver”，你手写 `Class.forName` 只是重复做同一件事。

### DriverManager
![[Pasted image 20260808194525.png]]**`META-INF`**，是 JAR 文件规范里的一个**标准元数据目录**，意思是 "metadata information"。它不是导入项目时才生成的，而是打包 jar 的时候就已经写在压缩包里面了，只是 IntelliJ 展开依赖时你才看得到。
![[Pasted image 20260808195304.png]]

### Connection
![[Pasted image 20260808195359.png]]
![[Pasted image 20260808195500.png]]
### Statement
![[Pasted image 20260808195853.png]]
### ResultSet
![[Pasted image 20260808200338.png]]

### PreparedStatement
![[Pasted image 20260808201432.png]]
你的密码输入这个：
![[Pasted image 20260808202247.png]]
![[Pasted image 20260808202125.png]]

你会发现where恒等于true，那么你都会返回所有结果，此时登陆就成功了

![[Pasted image 20260808202259.png]]