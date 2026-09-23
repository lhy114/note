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
![[Pasted image 20260923101039.png]]