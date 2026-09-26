# 进程和线程
## 进程与线程的基本概念
![[Pasted image 20260921100200.png]]![[Pasted image 20260921100340.png]]

## 并行和并发
![[Pasted image 20260921100630.png]]
![[Pasted image 20260921100737.png]]
![[Pasted image 20260921100751.png]]

## 同步和异步
![[Pasted image 20260921101131.png]]![[Pasted image 20260921101730.png]]

## Thread 和 Runnable之间的关系
![[Pasted image 20260921102559.png]]![[Pasted image 20260921103237.png]]
## 线程运行的原理
[[JVM#虚拟机栈]]![[Pasted image 20260921104318.png]]

每一个线程就是一个栈， 栈里面有很的栈帧，这个栈帧就是一个方法， 方法里面就是对应的局部变量等参数

### 上下文切换
![[Pasted image 20260921112318.png]]

## park线程
![[Pasted image 20260921160018.png]]

```
Thread t = new Thread(() -> {

    System.out.println("开始");

    LockSupport.park();

    System.out.println("继续执行");
});

t.start();

Thread.sleep(1000);

LockSupport.unpark(t);

线程 t

开始
 ↓
park()
 ↓
WAITING
 ↓
等待
 ↓
            main线程
                │
                │ unpark(t)
                ↓
        唤醒线程 t
                │
                ↓
             RUNNABLE
                │
                ↓
           继续执行
```

![[Pasted image 20260921160303.png]]

```
                    park()
                      │
          ┌───────────┴───────────┐
          │                       │
     interrupt=true          interrupt=false
          │                       │
          ▼                       ▼
      直接返回              看 permit
                                  │
                           ┌──────┴──────┐
                           │             │
                       有 permit       无 permit
                           │             │
                           ▼             ▼
                       直接返回         等待
```

## 守护线程与主线程
![[Pasted image 20260921160557.png]]

## 状态
### 五种状态
![[Pasted image 20260921160630.png]]

### 六种状态
![[Pasted image 20260921160652.png]]

# 并发之共享模型
## 临界区和竞态条件
![[Pasted image 20260922101444.png]]

![[Pasted image 20260922101535.png]]![[Pasted image 20260922101549.png]]

## synchronized 对象锁
![[Pasted image 20260922103216.png]]

## 变量的线程安全分析
![[Pasted image 20260922105223.png]]

## Monitor
### 对象头
![[Pasted image 20260923095201.png]]![[Pasted image 20260923095216.png]]

这里在说明一下 klass word一般是描述这个类的方法、变量等信息[[JVM#类加载阶段]]

### Monitor(锁)
![[Pasted image 20260923100011.png]]![[Pasted image 20260923100025.png]]

这里就说明了，obj相当于一个房间，当synchronized一个对象的时候，首先会更具这个对象的对象头里面的markword字段，查询关联的monitor，这个monitor会记录当前这个锁的拥有者，entrylist代表的是当前被阻塞的队列有哪些。

## 轻量级锁
[[并发编程_原理.pdf | 轻量级]]
![[Pasted image 20260923101039.png]]

这里有问题，并不是所有的栈帧都会保存一个锁记录。
不是所有栈帧。典型 HotSpot 实现中，锁记录属于“执行这次 `monitorenter` 的那个栈帧”：

- `synchronized (obj) { ... }`：属于包含这段同步块的栈帧，也就是你说的 B。
- `synchronized` 实例方法：属于该同步方法的栈帧。
- C 如果没有 `synchronized`，C 的栈帧里不会创建锁记录。
![[Pasted image 20260923102006.png]]

这里要提一句，如果加锁失败了，有两种情况，一个是自己再加锁了一次；另外一个情况是，其他线程已经持有该对象的轻量级锁，此时存在竞争，需要进行锁膨胀阶段
![[Pasted image 20260923102109.png]]

## 锁膨胀
![[Pasted image 20260923102251.png]]![[Pasted image 20260923103359.png]]

## 自旋优化
![[Pasted image 20260923103651.png]]
![[Pasted image 20260923103628.png]]

## 偏向锁
![[Pasted image 20260923103944.png]]
![[Pasted image 20260923104118.png]]
 ![[Pasted image 20260923105215.png]]
## 批量重偏向
![[Pasted image 20260923105757.png]]

## wait / notify
![[Pasted image 20260923114932.png]]![[Pasted image 20260923115959.png]]
这里起始想要说明的是，你要调用**wait和notify方法**，你必须要 **获得锁对象**，简单来说，至少要在synchronized这段代码里面才能调用

### sleep和wait的区别
![[Pasted image 20260923120151.png]]
但是他们的状态都是TIME_WAITING

![[Pasted image 20260923121516.png]]

## 模式
- **GuardedObject**：一个线程等待另一个线程给它一个**特定结果**，是**一对一、定向**的结果传递。
- **生产者-消费者**：生产者和消费者通过**共享队列**解耦，是**多对多、非定向**的任务/数据传递。
### 同步模式之保护性暂停
![[Pasted image 20260923121652.png]]
join就是这样实现的

### 异步模式之生产者/消费者
![[Pasted image 20260923152936.png]]


## Park和Unpark
![[Pasted image 20260923191935.png]]
![[Pasted image 20260923191753.png]]

![[Pasted image 20260923191950.png]]
![[Pasted image 20260924091731.png]]

![[Pasted image 20260924091810.png]]![[Pasted image 20260924091819.png]]![[Pasted image 20260924091838.png]]![[Pasted image 20260924091850.png]]

## 线程的活跃性
### 死锁
![[Pasted image 20260924093417.png]]

### 活锁
![[Pasted image 20260924094804.png]]

### 饥饿问题
![[Pasted image 20260924095349.png]]

## ReentrantLock
![[Pasted image 20260924095719.png]]
![[Pasted image 20260924095846.png]]
### 可重入
![[Pasted image 20260924095949.png]]

### 可打断
![[Pasted image 20260925151154.png]]

### 公平锁
reentrantlock默认是不公平锁，可以修改为公平锁

### 条件变量
![[Pasted image 20260925154546.png]]




# 共享模型之内存
![[Pasted image 20260926085615.png]]
## 可见性
定义：一个数据被A线程进行修改，但是B线程任然读取的是旧值。
案例：线程A和线程B共享一个静态变量flag，线程B通过while 读取这个flag变量，此时一段时间后线程A读取并修改这个变量flag，B此时读不到新值，因为对应的flag值由于受到JIT的优化，将其缓存到CPU缓存中，线程直接从CPU缓存读取，而不是堆里面

```
public class Test17 {  
  
    static boolean flag = true;  
  
    static void main() {  
        new Thread(()->{  
            System.out.println("线程运行");  
            while(flag){  
                //....  
            }  
            System.out.println("线程停止");  
        }).start();  
  
        try {  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
  
        flag = false;  
    }  
}
```

解决：
![[Pasted image 20260926090551.png]]![[Pasted image 20260926091041.png]]

“`synchronized` 提供的是 Java 层面的同步语义，而这个语义最终需要 JVM/JIT 和底层硬件来实现。因此，`synchronized` 本身并不是在物理意义上禁止缓存，而是规定了哪些缓存、寄存器复用和代码优化不能影响程序所观察到的结果。”

## 有序性
![[Pasted image 20260926100338.png]]
![[Pasted image 20260926100659.png]]![[Pasted image 20260926101101.png]]

## volatile 原理
![[Pasted image 20260926101611.png]]![[Pasted image 20260926101707.png]]![[Pasted image 20260926101831.png]]

## double check locking

