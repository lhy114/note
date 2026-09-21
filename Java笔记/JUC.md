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