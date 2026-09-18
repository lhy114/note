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
那么，此时我们的虚引用对象Cleaner就会进入引用队列中去，然后通过一个线程就会监听到这个队列有了一个Cleaner对象，此时就会调用这个对象的cleaner方法， 通过unsafe对象的free释放这个直接内存。


> 弱引用也能知道对象被回收，为什么不用弱引用？

区别：

**弱引用**
```
对象只剩弱引用
        ↓
GC
        ↓
对象被清除
```

它关注：

> “这个对象应该被回收了”

---

**虚引用**

```
对象已经进入回收阶段
        ↓
通知我
        ↓
我执行额外清理
```

它关注：

> “这个对象已经死了，我需要做一些善后工作。”

例如：

- 释放文件句柄
- 释放 Native Memory
- 释放操作系统资源

| 类型  | get()  | GC态度   | 用途              |
| --- | ------ | ------ | --------------- |
| 强引用 | 可以     | 不回收    | 普通对象            |
| 软引用 | 可以     | 内存不足回收 | 缓存              |
| 弱引用 | 可以     | GC时回收  | 弱缓存、WeakHashMap |
| 虚引用 | 永远null | 回收后通知  | 资源清理            |
强引用：
    我需要这个对象

软引用：
    我想留着，但内存不够可以丢

弱引用：
    我只是暂时关联，不影响GC

虚引用：
    我不要对象，我只想知道它什么时候死，然后做清理

#### 终结器引用

**终结器引用（Final Reference）**可以理解成 JVM 为了处理 Java 的 `finalize()` 机制而存在的一种特殊引用。

**finalize机制为：当一个对象第一次被 GC 判定为“不可达”，但这个对象重写了 `finalize()` 方法时，JVM 不立即回收它，而是先安排执行一次 `finalize()`，然后再决定是否真正回收。**

```
       User对象
          ↓
   第一次GC发现不可达
          ↓
   终结器引用 / Finalizer机制
          ↓
      执行 finalize()
          ↓
     再次进行可达性判断
          ↓
      ┌─────┴─────┐
      ↓           ↓
   又可达        仍不可达
      ↓           ↓
   对象复活       真正回收
```

它的缺点如下：不可靠、执行时间不可控，而且可能导致对象复活，所以现代 Java 已经不推荐使用它。


## 垃圾回收算法
### 标记清楚
![[Pasted image 20260915111352.png]]
优点：速度快， 清楚的过程只需要记录哪些被回收的对象空间的起始地址。
缺点：产生内存碎片。

### 标记整理
![[Pasted image 20260915111536.png]]
缺点：需要空间移动很耗费时间
好处：没有内存碎片
### 复制
![[Pasted image 20260915111717.png]]
![[Pasted image 20260915111728.png]]

缺点：就是占据双倍的内存空间
好处：没有内存碎片


## 分代回收
![[Pasted image 20260915113004.png]]

1. 对象首先会分配到新生代的伊甸园空间中
	1. 分配成功，后续就不管了。
2. 分配失败，此时会出发minor gc机制，伊甸园和幸存区首先会执行一次gc，将可以回收的对象进行释放，将幸存下来的对象放在幸存区to中（使用copy算法），然后存活的对象寿命+1；
	1. 注意，minor gc会触发stop the world 机制（STW），暂停其他用户线程，等待垃圾回收完毕之后，用户线程才会回复运行
	2. 另外，如果此时存活的对象的寿命达到阈值，会晋升到老年代中，一般来说最大寿命为15（4bit才存储对象的寿命）
3. 如果此时新生代空间还是不足（执行了minor gc机制之后），那么就会触发full gc。
4. 如果任然不够，就会抛出异常

```
对象创建
   ↓
优先分配到 Eden
   ↓
Eden 空间足够？
   ├── 是
   │    ↓
   │  对象正常运行
   │    ↓
   │  后续根据可达性决定是否成为垃圾
   │
   └── 否
        ↓
     Minor GC
        ↓
   STW，暂停用户线程
        ↓
Eden + From Survivor
        ↓
    可达性分析
        ↓
  垃圾对象被回收
        ↓
  存活对象复制到 To Survivor
        ↓
   对象年龄 +1
        ↓
  是否满足晋升条件？
      ├── 是 → Old Generation
      └── 否 → Survivor
        ↓
   Minor GC结束
        ↓
是否仍然无法满足分配/晋升需求？
        ├── 否 → 正常运行
        │
        └── 是
             ↓
          Full GC
             ↓
        仍无法满足？
             ├── 否 → 正常运行
             └── 是 → OOM
```
注意如果放入的这个对象很大，整个新生代都放不下，就会直接进入到老年代进行尝试，不会执行gc操作。
OOM 通常是当前线程抛出异常，但是内存不足也会影响其他线程


### 相关VM参数
![[Pasted image 20260915113954.png]]

## 垃圾回收器
https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/

### 串行（Serial GC）
Serial GC 的所有 GC 工作基本都由单个 GC 线程完成。

1. 单线程
2. 堆内存较小，适合个人电脑
![[Pasted image 20260915184214.png]]
### 吞吐量优先 （Parallel GC / Parallel Old）
1. 多线程
2. 堆内存较大，多核cpu
3. 让单位时间内， STW的时间最短
![[Pasted image 20260915184237.png]]
这里的时候，用户线程是暂停的

### 响应时间优先 （CMS / G1） concurrent mark sweep
1. 多线程
2. 堆内存较大，多核cpu
3. 尽可能让单次 STW 的时间最短

concurrent ：并发的， parallel：并行

注意是老年代的
![[Pasted image 20260915184750.png]]
第三个参数是为了应多在运行的时候产生了新的垃圾，而此时正在做并发清理，只能等到下一次垃圾清理的时候才能删除垃圾信息，因此，为了避免堆内存溢出，我们会设置一个比例，到达一定比例后就要执行一次垃圾回收。

第四个参数重新标记的过程中，新生代在运行过程中也会产生很多的垃圾，而此时你做一个重新标记就会做很多无用功，因此我们在重现标记之前清理一下。


CMS最大的一个问题：

> **它是 Mark-Sweep，不进行整理/压缩。**

也就是说：

```
[对象][垃圾][对象][垃圾][对象]
```

清除以后：

```
[对象][空闲][对象][空闲][对象]
```

容易产生：

> **内存碎片。**

最后可能出现：

```
总空闲内存 = 1GB

但是没有连续的 500MB

↓

无法分配大对象
```

于是可能发生：

```
Concurrent Mode Failure
↓
Full GC
↓
STW
```

CMS 因此逐渐被 G1 替代。Oracle 也明确将 G1 定位为 CMS 的长期替代方案。



### G1 《重点，但是没有掌握，这里的教程讲的很烂》
![[Pasted image 20260915190740.png]]

#### G1 垃圾回收阶段
![[Pasted image 20260915191028.png]]
以前：

```
整个堆

┌───────────────┐
│ Young         │
├───────────────┤
│ Old           │
└───────────────┘
```

G1：

```
┌────┬────┬────┬────┐
│ R1 │ R2 │ R3 │ R4 │
├────┼────┼────┼────┤
│ R5 │ R6 │ R7 │ R8 │
├────┼────┼────┼────┤
│ R9 │R10 │R11 │R12 │
└────┴────┴────┴────┘
```

整个 Heap 被划分成很多：

> **Region**

每个 Region 可以在不同时间承担不同角色。例如

```
R1 → Eden（伊甸园）
R2 → Eden
R3 → Survivor （存活区）
R4 → Old （老年区）
R5 → Old
R6 → Free
R7 → Humongous（大对象）
...
```


#### 为什么G1能够控制停顿时间呢

假设：

```
整个 Heap：

100 个 Region
```

现在：

```
R1：垃圾 90%
R2：垃圾 80%
R3：垃圾 70%
R4：垃圾 10%
R5：垃圾 5%
...
```

G1 会发现：

```
R1 很值得回收
R2 很值得回收
R3 很值得回收
```

所以：

```
R1
R2
R3
...
```

优先回收。

这就是：

> **Garbage First**

---

但是 G1还有一个非常重要的目标：

```
-XX:MaxGCPauseMillis=200
```

意思是：

> 希望一次 GC 的停顿时间目标大约控制在 200ms。

假设 G1 根据历史数据估计：

```
回收一个 Region ≈ 20ms
```

那么它可能选择：

```
200 / 20 ≈ 10 个 Region
```

于是：

```
本次 GC：

R1
R2
R3
R5
R8
R10
R12
R15
R18
R20
```

而不是：

```
整个 Old Generation
```

因此 G1 本质上是在做：

> **垃圾回收收益 + 停顿时间预算之间的动态平衡。**


#### Young GC

只处理年轻代相关 Region：

```
Eden
+
Survivor
```

例如：

```
Eden：

R1
R2
R3
R4
```

发生 Young GC：

```
R1 R2 R3 R4
 ↓  ↓  ↓  ↓
存活对象
 ↓
Survivor / Old
```

特点：

> **STW**

但通常比较短。

#### Mixed GC
它会同时回收：

```
Young Region
+
部分 Old Region
```

例如：

```
Young：
R1 R2 R3

Old：
R10 R12 R15 R18 R20
```

Mixed GC可能：

```
R1
R2
R3
R10
R12
R15
```

一起回收。

所以：

> **Mixed GC = 年轻代 + 一部分老年代 Region 一起回收。**

这就是 G1 和传统分代 GC 很大的一个区别。

#### 执行周期
```
                 G1
                  │
          ┌───────┴───────┐
          │               │
       Young GC       并发标记周期
          │               │
          │        ┌──────┴──────┐
          │        │             │
          │     标记 Old       找出垃圾
          │        │             │
          └────────┴─────────────┘
                       ↓
                  Mixed GC
                       ↓
                  Old 被回收
```


### G1 字符串去重
![[Pasted image 20260916094109.png]]

### G1 类卸载
![[Pasted image 20260916094226.png]]

### G1 回收巨型对象
![[Pasted image 20260916094413.png]]

incoming

### 动态调整阈值
![[Pasted image 20260916094543.png]]



## GC调优 略， 这里相当于一个领域了，就看八股文会问什么就行了


# 类加载与字节码技术
## 类文件结构
![[Pasted image 20260916103345.png]]

| 区域                    | 作用                                        |
| --------------------- | ----------------------------------------- |
| `magic`               | 固定为 `CAFEBABE`，表示这是 Class 文件              |
| `minor_version`       | 次版本号                                      |
| `major_version`       | 主版本号，决定最低需要的 JVM 版本                       |
| `constant_pool_count` | 常量池条目数量加一                                 |
| `constant_pool[]`     | 保存字面量、名称、描述符和符号引用                         |
| `access_flags`        | 类的访问权限和修饰符，例如 `public`、`final`、`abstract` |
| `this_class`          | 指向常量池，表示当前类的完整名称                          |
| `super_class`         | 指向常量池，表示父类名称                              |
| `interfaces_count`    | 当前类直接实现的接口数量                              |
| `interfaces[]`        | 接口名称的常量池索引数组                              |
| `fields_count`        | 字段数量                                      |
| `fields[]`            | 字段定义表                                     |
| `methods_count`       | 方法数量                                      |
| `methods[]`           | 方法定义表                                     |
| `attributes_count`    | 类级属性数量                                    |
| `attributes[]`        | 类级附加信息，例如源文件名、内部类、注解等                     |

### 魔数和版本
![[Pasted image 20260916103525.png]]![[Pasted image 20260916103601.png]]
### 常量池
[[二进制文档]]
常量池保存的是类定义过程中需要反复引用的“名字、类型、常量和符号引用”。完整的字段声明、方法声明、访问权限和字节码，则由类文件的其他区域保存，并通过常量池索引连接起来。


| 类别   | 示例                                          | 作用                    |
| ---- | ------------------------------------------- | --------------------- |
| 字面量  | `"Hello World"`、`123`、`3.14`                | 程序中的常量                |
| 名称   | `java/lang/System`、`main`                   | 类名、字段名、方法名            |
| 描述符  | `()V`、`(Ljava/lang/String;)V`               | 参数类型和返回值类型            |
| 类和接口 | `Class -> Utf8`                             | 指向具体类名                |
| 字段引用 | `Fieldref -> Class + NameAndType`           | 例如 `System.out`       |
| 方法引用 | `Methodref -> Class + NameAndType`          | 例如 `println(String)`  |
| 动态机制 | `MethodHandle`、`MethodType`、`InvokeDynamic` | `invokedynamic` 等高级机制 |
| 模块信息 | `Module`、`Package`                          | Java 模块系统使用           |

![[Pasted image 20260916110924.png]]
### 访问标识和继承信息
![[Pasted image 20260916110905.png]]

### 成员变量
![[Pasted image 20260916110942.png]]

### Method信息和附加属性


### 条件判断
![[Pasted image 20260917094719.png]]![[Pasted image 20260917094736.png]]
### 后续内容

![[Pasted image 20260917095428.png]]

![[Pasted image 20260917095547.png]]

我们做一个简单的回顾， 首先 10 被压入到局部变量表1中（bipush、istore1），然后将a压入到运行栈中（iload1），再在局部变量表1中进行+1（iinc），然后再在局部变量表1中进行+1（iinc），此时将a压入到运行栈中（iload），最后执行相加得到10+12=22，然后将局部变量a（12）再次压入到栈中，并对方法表中的a进行--，最后相加得到22+12=34

**1. 常量池，通常指 Class 文件常量池**

它是 `.class` 文件中的一张静态表，属于编译期产物，不是运行时对象。

里面主要保存：

- 字面量：整数、浮点数、字符串字面量等
- 类和接口的全限定名
- 字段名和字段描述符
- 方法名和方法描述符
- `Fieldref`、`Methodref`、`InterfaceMethodref`
- `NameAndType`
- 后来还可能有 `MethodHandle`、`MethodType`、`InvokeDynamic` 等

它本质上保存的是“符号引用”和描述信息，不直接保存最终内存地址。比如代码里写：

```
System.out.println("hello");
```

Class 文件中记录的不是 `System.out` 的真实对象地址，而是一个类似：

```
Class: java/lang/System
Field: out
Class: java/io/PrintStream
Method: println
String: hello
```

这些条目通过索引互相引用。

**2. 运行时常量池**

运行时常量池是 Class 文件常量池在 JVM 运行时的表示，存放在**方法区**中，每个类或接口一份。

类加载时，JVM 会把 Class 文件常量池中的内容解析、组织成运行时常量池。它的特点：

- 保存符号引用，例如类名、字段名、方法名
- 很多引用是**惰性解析**的，第一次真正用到时才解析
- 解析成功后，通常会把真实引用缓存起来
- `String` 字面量最终会关联到堆中被 intern 的 `String` 对象
- 一个类对应的运行时常量池通常随类卸载而回收

这里要注意两个常见区别：

- **常量池**：Class 文件里的静态表
- **运行时常量池**：JVM 内存中的运行时结构
- **字符串常量池**：通常指字符串 intern 表，和运行时常量池不是一回事

字符串字面量的信息在 Class 文件常量池里，但真正的 `String` 对象在堆中，字符串常量池负责保存规范化的引用关系。

**3. 方法区**

方法区是 JVM 规范中的逻辑概念，被所有线程共享，主要存放类级别的数据：

- 类的元数据，例如类名、父类、接口、访问标志
- 字段信息和方法信息
- 方法的字节码和异常表等
- 运行时常量池
- 类变量，也就是静态变量
- 类加载器相关信息

方法区是规范定义，具体怎么实现由 JVM 决定。

HotSpot 的历史实现要区分：

- JDK 8 以前：方法区的一部分实现是永久代 `PermGen`
- JDK 8 开始：永久代被移除，类元数据主要放在本地内存中的 Metaspace
- 字符串常量和静态变量在不同 JDK 版本中的实际存放位置有过变化，因此面试中最好区分“JVM 逻辑概念”和“HotSpot 实现细节”

方法区可以近似理解为：

> 存放类本身，而不是存放对象的区域。

对象实例在堆里，方法区保存“这个类长什么样、有哪些方法、有哪些静态字段”等信息。

**4. 栈帧**

Java 虚拟机栈是线程私有的，每调用一次 Java 方法，就会创建一个栈帧，并压入当前线程的 Java 虚拟机栈。

一个栈帧通常包含：

- **局部变量表**
    
    - 保存方法参数和局部变量
    - 实例方法的第 0 个槽通常是 `this`
    - `long` 和 `double` 通常占两个槽
    - 槽位数量在编译期基本确定
    - 整数通常直接编码在字节码指令里
- **操作数栈**
    
    - 字节码执行时用来做计算和传递参数
    - 例如执行 `a + b` 时，先压入两个操作数，再执行加法指令
- **动态链接**
    
    - 当前方法所属类的运行时常量池引用
    - 字节码中的字段引用、方法引用通常先指向运行时常量池
    - 真正调用时再解析成实际的方法或字段
- **方法返回信息**
    
    - 正常返回地址
    - 异常返回处理信息
    - 同步方法还可能涉及锁状态

方法执行过程可以简化成：

```
调用方法
  -> 创建栈帧
  -> 将参数放入局部变量表
  -> 执行字节码
  -> 操作数栈参与计算
  -> 通过动态链接解析运行时常量池引用
  -> 方法返回
  -> 栈帧出栈
```

一句话总结：

> **Class 文件常量池是编译期的静态表；运行时常量池是它进入 JVM 后的运行时形式，放在方法区；方法区保存类级别信息；栈帧表示一次方法调用，并通过动态链接访问运行时常量池。**

![[Pasted image 20260917101952.png]]![[QQ20260917-102742.png]]

dup是赋值，pop static方法不用通过对象调用，可以直接通过类名调用
![[Pasted image 20260917104018.png]]

- `invokevirtual`
    
    - 普通类实例方法
    - 使用 vtable 做动态分派
- `invokeinterface`
    
    - 接口方法
    - HotSpot 通常通过 itable 或相关优化机制处理
- `invokespecial`
    
    - 构造方法、私有方法、`super` 调用
    - 不做基于实际类型的动态分派
- `invokestatic`
    
    - 静态方法
    - 没有接收者，不经过 vtable
- `invokedynamic`
    
    - Lambda、字符串拼接等
    - 通过调用点和方法句柄机制处理，不是直接用 vtable
- **`final`、`private`、`static` 方法**

- `static` 方法不参与对象多态，不需要 vtable 分派
- `private` 方法不能被继承和重写，通常走非虚调用
- `final` 方法不能被重写，JVM 和 JIT 往往可以确定目标，减少或绕过 vtable 查找
- `final` 方法也可能在 vtable 中占位，但运行时可进行去虚拟化优化


**和普通方法表、itable 的区别**
- **方法表**
    - 保存类中所有方法的元数据
    - 范围更全
- **vtable**
    - 主要服务于类继承体系中的虚方法分派
    - 重点是重写后仍使用相同槽位
- **itable**
    - 服务于接口方法分派
    - 因为一个类可以实现多个接口，接口布局不像单继承 vtable 那么直接


![[Pasted image 20260917104429.png]]

异常表
![[Pasted image 20260917104454.png]]

![[Pasted image 20260917104907.png]]

finally会将要执行的代码块放在try、catch块后面，注意如果exception、try抓不到的范围，finally也要进行执行，因此finally有三块
![[Pasted image 20260917105113.png]]

![[Pasted image 20260917105320.png]]

```
public class test {  
  
    public static void main(String[] args) {  
        System.out.println(test());  
    }  
  
    public static int test(){  
        int i = 10;  
        try {  
            return i;  
        }finally {  
            i = 20;  
        }  
    }  
}
```
![[Pasted image 20260917105936.png]]

![[Pasted image 20260917110950.png]]
![[Pasted image 20260917110854.png]]


为什么要有两份引用
**核心原因：`monitorenter` 会消耗掉操作数栈上的引用**，第一次消耗slot1的引用，`monitorenter` 需要从操作数栈顶部弹出一个对象引用，并给这个对象加锁。 那么你在解锁的时候就必须要要有一个对象引用执行monitorexit


## 编译期处理
![[Pasted image 20260918124831.png]]

### 默认构造器
![[Pasted image 20260918124852.png]]

### 自动拆箱机制
![[Pasted image 20260918124932.png]]

### 泛型擦除
![[Pasted image 20260918125121.png]]

### 可变参数
 ![[Pasted image 20260918125754.png]]
### foreach循环
![[Pasted image 20260918125854.png]]

### switch 字符串、枚举、try-catch、方法重写、匿名内部类

![[Pasted image 20260918130326.png]]

![[Pasted image 20260918130336.png]]

![[Pasted image 20260918130354.png]]![[Pasted image 20260918130405.png]]![[Pasted image 20260918131411.png]]
![[Pasted image 20260918131441.png]]
synthetic bridge 不可见，不会违反重写的规则
![[Pasted image 20260918131521.png]]



## 类加载
### 加载

![[Pasted image 20260918133602.png]]