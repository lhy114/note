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