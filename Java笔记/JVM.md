# 引言
定义: java virtual machine - java 程序的运行环境(java 二进制字节码的运行环境)
好处:
1. 一次编译,到处运行
2. 自动内存管理, 垃圾回收功能
3. 数组下标越界, 越界检查
4. 多台
![[Pasted image 20260914095813.png]]


![[Pasted image 20260914100404.png]]


# 内存结构
## 程序计数器(PC Register)
### 定义
Program Counter Register: 
**作用: 记录下一条JVM指令的地址**

![[Pasted image 20260914100948.png]]

流程是: java源代码被编译器写成java class的二进制字节码文件, 然后这些字节码文件会交给解释器转换为cpu读的懂的机器码, 此时整个程序就能够运行
**在程序运行过程中, 我们需要记录下一条指令的地址, 因此我们需要有一个东西来进行存储, 这就是PC的作用**.
### 作用和特点
特点: 
1. 线程私有
2. 不会存在内存溢出(PC 表示的是当前方法的字节码执行位置，而一个 Java 方法的字节码长度本身存在上限。在JVM虚拟机规范中, 规定一个方法的JVM字节码小于64kb, 也就是65536)

![[Pasted image 20260914101454.png]]
这幅图展示的是,为什么对于PC而言是线程是私有的, 如果说线程1和线程2共享同一个PC, 那么当线程1的时间片用完的时候,恰好执行到9这个位置, 此时PC需要记录10的地址, 那么线程2开始执行, 就会将自己的下一条JVM指令的地址覆盖到PC上, 那么当再次轮到线程1的时候, 线程就会无法知道上一次执行的地址了

**PC 的具体位数没有规定,具体 JVM 根据底层架构/实现决定内部表示**

## 虚拟机栈
### 定义
**虚拟机栈:每个线程运行时所需要的内存**
**栈帧:每一个方法运行所需要的内存, 一般要存储变量、参数、返回值**
**活动栈:代表当前正在执行的哪一个方法**
![[Pasted image 20260914103028.png]]

![[Pasted image 20260914102838.png]]

问题辨析:
1. 垃圾回收是否涉及栈内存?
	答: 不涉及，栈内存在每一次栈帧执行完毕之后，会释放对应起资源， 垃圾回收主要是处理的堆内存里面的东西， 所以不涉及栈内存。
2. 栈内存分配越大越好吗?
	答：不， 一个计算机的内存大小是固定的，如果我们栈内存设置的越大，那么你可用的栈数量就会减少，并发执行效率在一定程度上收到影响。 栈内存大小的设置，只会影响你调用其他方法的次数，这就是你写递归的时候，容易出现栈溢出的情况，导致一个方法被不断的压入到栈内存中。
3. 方法内的局部变量是否线程安全?
	答：视情况而辨。1. 如果方法内的变量没有逃离方法的作用访问，他就是安全的； 2. 如果这个局部变量引用了对象， 那么就需要**考虑**线程安全， 就是要看这个变量的位置在哪里，在堆里面？还是栈里面？



### 栈内存溢出
1. 栈帧过多导致栈内存溢出
2. 栈帧过大导致栈内存溢出（很少）

![[Pasted image 20260914111232.png]]

## 本地方法栈
本地方法栈，一般是指那些不使用java实现，底层使用C或者C++实现与计算机硬件进行直接调用的方法， 这些方法一般是用native进行什么， java直接调用。 

本地方法栈是 JVM 为执行 Native 方法准备的线程私有栈空间，它和虚拟机栈类似，都保存方法调用信息，但虚拟机栈服务 Java 方法，本地方法栈服务 C/C++ 等底层方法。在 HotSpot 中二者通常共享线程栈空间。

![[Pasted image 20260914111549.png]]

在Java中，native关键字用于声明一个方法是由本地代码（通常是C或C++）实现的。这意味着该方法的具体实现不是用Java编写的，而是由底层的本地代码提供。


## 堆
### 定义

**Heap 堆：通过new关键字，创建的对象都会使用堆内存**
**特点：**
	1. 线程之间是相互共享的， 堆中的对象都需要考虑线程安全问题
	2. 有垃圾回收机制
### 堆内存溢出
一般来说就是在一个对象的生命周期中，  如果在不断的访问，会导致GC无法进行回收， 此时你在不断向堆内存里面添加东西， 此时这个堆空间就会挤满，放不下数据了。
### 堆内存诊断
![[Pasted image 20260914112754.png]]
还有一个叫做jvirsualvm

## 方法区

![[Pasted image 20260914115612.png]]![[Pasted image 20260914115839.png]]

```
                 JVM 规范中的逻辑概念
                         │
                       方法区
                         │
          ┌──────────────┴──────────────┐
          │                             │
      JDK 7及以前                    JDK 8+
          │                             │
      PermGen 永久代                 Metaspace 元空间
          │                             │
      JVM堆内存的一部分？             Native Memory
      （准确说：JVM管理的区域）        （本地内存）
```

### 什么是永久代
PermGen 全称：
> **Permanent Generation，永久代**

它是 HotSpot JVM 在 **JDK 7 及以前**用于实现方法区的一块内存区域。

之所以叫“永久代”，是因为这里面主要放的是一些**生命周期与类加载器相关的数据**，例如：

```
类的元数据
├── 类的信息
├── 方法的信息
├── 字段的信息
├── 方法字节码相关信息
├── 常量池相关信息
└── ...
```

这些东西不像普通 Java 对象那样随着一次方法调用结束就消失。

例如：

```
public class User {
    private String name;

    public void sayHello() {
        System.out.println("hello");
    }
}
```

当 JVM 加载 `User.class` 时，需要保存关于 `User` 这个类的大量信息。

这些类元数据在早期 HotSpot 中主要放在：

```
PermGen（永久代）
```

因此它被称为 **Permanent Generation**。

### 为什么不用永久代了
这里是理解 JDK 8 变化的关键。

永久代有一个比较明显的问题：

> **大小不好控制，而且容易出现 `OutOfMemoryError: PermGen space`。**

例如：

```
PermGen
容量：固定/受限
       ↓
加载大量 Class
       ↓
PermGen 不够
       ↓
OutOfMemoryError: PermGen space
```

尤其是：

- 大量动态生成 Class
- 大量使用动态代理
- 热部署
- 应用服务器
- 大量第三方框架

都可能导致类元数据不断增加。

于是 JDK 8 做了一个重要改变：

> **HotSpot 移除了 PermGen，使用 Metaspace（元空间）来存放类元数据。**

### 方法区内存溢出
![[Pasted image 20260914121416.png]]

## 常量池
![[Pasted image 20260914181344.png]]

### StringTable 
#### 基础性质

`intern()` 是 Java `String` 提供的一个方法，核心作用可以一句话记：

> **让一个字符串对象去“字符串常量池（String Pool / StringTable）”中寻找对应的字符串，并返回池中的那个引用。**

先看最典型的例子：

```
String s1 = new String("hello");
String s2 = s1.intern();

System.out.println(s2 == "hello");  // true
```


![[Pasted image 20260914182640.png]]

首先当源代码被编译成字节码文件.class之后， a、b、ab都是属于常量池里面的信息（注意， 这里的常量池不止有变量的信息， 还有类的各种方法等）， 然后当程序运行起来的时候， 此时当到达这段 String s1 = "a"这段代码的时候， 此时才会把a 符号变为“a”字符串对象。 **由于java存在一个String table的东西**，当第一次看到这个的时候，就会在串池里面创建一个“a”的字符串对象。

String s4 = s1 + s2， 经过反编译之后的结果，我们可以看到他是经历了一个new String("ab")， 而这个对象是在堆内存里面所以 s3 == s4 为false

![[Pasted image 20260914183556.png]]
此时s3 == s5 为true

![[Pasted image 20260914183824.png]]![[Pasted image 20260914184311.png]]
哦，对了值了返回的是串池的对象，所以第一个s2 == x为true

![[Pasted image 20260914184505.png]]

#### String Table的位置
![[Pasted image 20260914185209.png]]

#### StringTable 垃圾回收
有垃圾回收机制

#### StringTable 性能调优
![[Pasted image 20260914190903.png]]


## 直接内存
![[Pasted image 20260914191104.png]]
![[Pasted image 20260914191506.png]]
![[Pasted image 20260914191454.png]]

第一幅图，是我们传统读取文件的一个过程， 磁盘文件首先要写入到系统缓冲区之后，然后通过java的字节缓冲区才能完成后续的读或者写操作，这就是的对于同一分文件，两个缓冲区都会存有对应的数据， 这就造成了内存的紧张（相当于系统一份文件，jvm里面一份文件， 本来你只需要拷贝一次， 但是实际做了两次传输）。 因此我们不如就直接使用直接缓冲区， 减少了一次传输过程

### 直接内存回收
**直接内存的回收是通过**

直接内存（Direct Memory）和堆内存最大的区别就在这里：

> **直接内存不属于 JVM 堆，所以不能直接依赖普通的 GC 来回收。**

但它又不是“完全不会自动回收”。Java 有一套机制会在**对应的 Java 对象不可达后，由 GC 触发间接回收直接内存**。

以最典型的 `ByteBuffer.allocateDirect()` 为例：

```
ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
```

大概是：

```
        JVM 堆
┌──────────────────────┐
│ DirectByteBuffer对象  │
│                      │
│ 记录着直接内存地址 ───────┼──────┐
└──────────────────────┘      │
                              ↓
                     直接内存（Native Memory）
                     ┌────────────────┐
                     │   1024 bytes   │
                     └────────────────┘
```

这里实际上存在**两个东西**：

```
① DirectByteBuffer Java对象
        ↓
     在堆中

② 真正的数据
        ↓
     在直接内存中
```

---

### 那么怎么回收？

假设：

```
ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
```

后来：

```
buffer = null;
```

此时：

```
堆：
DirectByteBuffer对象
       ↓
   不可达
```

GC 发现这个 `DirectByteBuffer` 对象已经没有引用了。

但是：

> GC 不能简单地把它占用的直接内存当成堆对象一起清理。

所以 `DirectByteBuffer` 内部关联了一个 **Cleaner**，用于在对象被回收的过程中释放对应的 Native Memory。

可以粗略理解成：

```
DirectByteBuffer
       ↓
    Cleaner
       ↓
释放 Direct Memory
```

因此整体过程是：

```
DirectByteBuffer
       ↓
Java对象变成不可达
       ↓
GC发现它
       ↓
Cleaner执行清理
       ↓
释放Native Memory


GC 负责发现 DirectByteBuffer 不再使用；Cleaner 负责触发清理动作；底层通过 Unsafe 等机制真正释放 Native Memory。

```

![[Pasted image 20260914192718.png]]
![[Pasted image 20260914192923.png]]

```
ByteBuffer.allocateDirect()
        ↓
创建 DirectByteBuffer（Java对象，在堆中）
        ↓
Unsafe.allocateMemory()
        ↓
申请 Direct Memory（堆外内存）


DirectByteBuffer
      ↓
不再被任何强引用指向
      ↓
GC发现它不可达
      ↓
Cleaner / PhantomReference 机制发现它可以清理
      ↓
Cleaner执行 cleanup()
      ↓
Unsafe.freeMemory(address)
      ↓
Direct Memory 被释放
```


# 垃圾回收
## 如何判断对象可以回收
### 引用计数法
每个对象维护一个“被引用次数”，引用数变成 0，就认为这个对象可以回收。 每次引用一个对象的时候，它的引用次数+1，没有引用的时候次数-1。但是它纯在一个循环引用的问题，如下图所示，A引用B，B引用A，没有其他的对象引用他们俩，此时A和B的次数就永远就是1了，此时就无法被垃圾回收删除。
![[Pasted image 20260915093721.png]]

### 可达性分析算法
**可达性分析算法（Reachability Analysis）是 Java 垃圾回收判断“一个对象是否还能被使用”的核心思想。它不数“有多少个引用”，而是判断“从 GC Roots 出发，能不能找到这个对象”。**

假设有：

```
User user = new User();

user为局部变量，它在线程栈里面， User是一个对象，他是存在于一个堆里面
```

可以理解成：

```
GC Root
   │
   ↓
 user
   │
   ↓
 User对象
```

`user` 这个局部变量属于 **GC Root**。

从 GC Root 出发：

```
GC Root → user → User对象
```

能够找到 `User对象`。

所以：

> **User对象是可达的 → 不能回收。**


**常见的GC Root有哪些呢？**
1. 虚拟机栈中的引用
2. 方法区的静态变量
3. 方法区中常量引用对象
4. JNI中的引用，也就是Native代码持有的Java对象引用
5. 活跃线程
6. 初始的一些依赖库的加载，例如Object


### 四种引用
![[Pasted image 20260915101405.png]]

![[Pasted image 20260915101440.png]]

#### 强引用
一般来说强引用存在的形式就是，例如User a = new User()， 此时a就相当于一个强引用对象。
```
栈
┌──────────────┐
│ user ────────┼──────┐
└──────────────┘      │
                      ↓
                   User对象（堆）
```

`user` 对 `User` 对象的引用，就是**强引用**。如果此时user = null， 由于不可达：
```
GC Root
   ↓
 user → null

 User对象
```

此时User就变成了不可达对象，于是它就具备了被GC回收的资格。

#### 软引用

```
SoftReference<User> ref = new SoftReference<>(new User());
```

此时整个可达性链路变成了：
```
GC Root
   ↓
  ref
   ↓
软引用
   ↓
User对象
```

![[Pasted image 20260915102155.png]]

它的回收规则是：如果一个对象只通过软引用可达，**那么 JVM 在内存不足、需要回收空间时**，可以回收这个对象。

```
1. User user = new User();
SoftReference<User> softRef = new SoftReference<>(user);

此时有两个引用， 一个强引用；另外一个是弱引用

GC Root
   │
   │ 强引用
   ↓
 user
   │
   ↓
 User对象
 
 和
 
 GC Root
   │
   │ 强引用
   ↓
softRef
   │
   │ 软引用
   ↓
 User对象

2. user = null;
此时就只有一个软引用了，那么User对象被回收的情况是

User对象
   ↓
只剩软引用
   ↓
发生GC
   ↓
JVM发现内存压力较大
   ↓
可以清除软引用指向的User
   ↓
User对象最终被回收
```

#### 弱引用

```
User user = new User();
WeakReference<User> weakRef = new WeakReference<>(user);

GC Root
   |
   | 强引用
   ↓
 user
   |
   ↓
 User对象
 
 GC Root
   |
   ↓
weakRef
   |
   | 弱引用
   ↓
User对象

执行user = null
GC Root
   |
   ↓
weakRef
   |
   | 弱引用
   ↓
User对象

如果此时发生了GC，那么就会直接将弱引用直接回收，不会像软引用一样还要判断是否内存紧不紧张
```

#### 虚引用
前三种引用：
```
强引用：
    可以通过引用访问对象

软引用：
    可以通过 get() 获取对象

弱引用：
    可以通过 get() 获取对象
```

例如：

```
weakRef.get();
```

可以得到对象。

但是虚引用：

```
PhantomReference<User> phantomRef =
        new PhantomReference<>(user, queue);
```

调用：

```
phantomRef.get();
```

永远返回：

```
null
```

也就是说：

```
虚引用 ─────→ User对象

但是：
phantomRef.get() = null
```


```
User user = new User();
ReferenceQueue<User> queue = new ReferenceQueue<>();
PhantomReference<User> ref = new PhantomReference<>(user, queue);

此时的引用为
GC Root

user
 |
 ↓
User对象


ref
 |
 ↓
虚引用
 |
 ↓
User对象

然后你现在将user = null， 此时User没有了强引用，只剩下了虚引用了，GC执行发现对象不可达。

User对象
    ↓
准备回收
    ↓
phantomRef 放入 ReferenceQueue
    ↓
程序收到通知

Reference<? extends User> r = queue.poll();
```

![[Pasted image 20260915110208.png]]

回到这个Cleaner是如何回收直接内存的， 我们知道直接内存是有一个Cleaner 虚引用到一个ByteBuffer对象， 然后这个ByteBuffer对象就会申请一个直接内存， 当这个ByteBuffer对象不可达的时候，此时就会被GC所回收，但是你就会注意到，这个直接内存没有无法被管理回收
![[Pasted image 20260915110415.png]]
那么，此时我们的虚引用对象Cleaner就会进入引用队列中去，然后通过一个线程就会监听到这个队列有了一个Cleaner对象，此时就会调用这个对象的cleaner方法， 通过unsafe对象的free释放这个直接内存