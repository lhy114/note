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
Program Counter Register: 
**作用: 记录下一条JVM指令的地址**

![[Pasted image 20260914100948.png]]

流程是: java源代码被编译器写成java class的二进制字节码文件, 然后这些字节码文件会交给解释器转换为cpu读的懂的机器码, 此时整个程序就能够运行
**在程序运行过程中, 我们需要记录下一条指令的地址, 因此我们需要有一个东西来进行存储, 这就是PC的作用**.

特点: 
1. 线程私有
2. 不会催在nei cun