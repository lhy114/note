### "\t"
制表符，将字符串补充到长度为8或者8的倍数。

### 运算的顺序
public class demo1 {  
    public static void main(String[] args){  
        int a = 10, b = 10, c = 10;  
        System.out.println("a+b="+(a+b));  
    }  
}

### a的acsii码为97（十进制）， A的acsii码为71

### Long和float 类型的变量需要在后面添加L和F才能正确的赋值

### 小驼峰和大驼峰命名法
![[Pasted image 20260715105551.png]]

### Scanner的简单用法（当你再次看到这个资料的时候，请你认真查询一下Scanner的用法以及还有那些是读入数据的）
import java.util.Scanner;  ### 倒入包
public class demo1 {  
    public static void main(String[] args){  
        Scanner sc = new Scanner(System.in); ### 创建对象  
        int a = sc.nextInt();  ### 读取数据
        System.out.println("a:"+a);  
    }  
}


### 创建项目的结构
![[Pasted image 20260715110238.png]]
**class:** 最小的副用单元

**package**：当class 的数量有多的时候，将class聚在一起。
- **命名冲突** — 不同人写的 `User.java` 怎么区分？
- **内聚分组** — 相关的类（比如所有和订单相关的）放在一起，维护和查找都方便。
com.example.order
  ├── Order.java
  ├── OrderItem.java
  └── OrderService.java

**Module**：当package多了之后，你发现这写package可以组成一个完整的子系统，例如支付模块，用户模块、库存模块。

### 隐式转换（自动类型提升，小变到大；byte short char，在运算的时候优先提升为int）；
byte a = 10; byte b =10;  c = a + b；此时c为int类型; 'a' + 'b' = 195,    (char)('a'+'b') = Ã

### 强制转换 double a = 12.3; int b = (int)a; 注意boolean不能和任何的数值类型进行任何转换，无论是隐式还式显式

![[Pasted image 20260715115332.png]]
![[Pasted image 20260715115808.png]]

### 逻辑运算符（& | ^ !）和短路逻辑运算符（&& ||）

### 三元运算符  关系表达式？表达式1 : 表达式2

### Random 随机生成\[a, b)的数据

Random r = new Random();  
int number = r.nextInt(100) + 1; # r.nextInt(100)生成\[0,100)的范围, +1 之后就是1到100的范围

### 数组的地址值
![[Pasted image 20260716093749.png]]

### 快速生成数组的遍历方式，数组名.fori（数字.fori也是的）

### 数组的默认初始化
![[Pasted image 20260716095122.png]]

### double 类型进行比较的时候注意事项
![[Pasted image 20260716100514.png]]
如果说要用== 的时候，我们需要利用epsilon进行比较

```
double a = 0.1 + 0.2;       // 0.30000000000000004
double b = 0.3;              // 0.3

a == b                       // false —— 数学上应该相等，但 bits 不一样

// 用 epsilon 判断
double eps = 1e-9;
Math.abs(a - b) < eps        // true ✅
```

### java内存分配
![[Pasted image 20260716101359.png]]

### 数组的地址，你就把这个例子当成一个c++的指针
![[Pasted image 20260716102214.png]]

### 方法的重载（overload），重载的形参匹配是编译器的事情，JVM 在运行时只是忠实地执行编译时已经选好的那个方法签名。真正由 JVM 在运行时根据类型做决策的，是**重写（Override）**。
```
overload执行的过程是在编译过程中运行的，并不是由JVM进行的。
编译器首先达到function（x）这个调用，首先执行了一下的步骤：
1. 查看x的申明类型
2. 在类中找到所有叫function的方法
3. 然后按照“最精确匹配”的规则选择一个适合的方法
   
void bar(int a)    { ... }
void bar(double a) { ... }

int x = 10;
bar(x);   // 编译器看到 x 是 int → 选 bar(int)
//          编译后字节码里已经固定调用 bar(int)
//          运行时不会再去考虑 bar(double)


重写（Override）是子类对父类中允许继承的方法重新实现。
class Animal {
    void speak() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    @Override
    void speak() {
        System.out.println("Woof");
    }
}

class Cat extends Animal {
    @Override
    void speak() {
        System.out.println("Meow");
    }
}

Animal a1 = new Dog();
Animal a2 = new Cat();

a1.speak();   // Woof  — 编译时类型是 Animal
a2.speak();   // Meow  — 但运行时 JVM 根据真实类型分派

编译阶段（编译器）只是关注你写的对不对，而运行阶段（JVM虚拟机通过加载.class文件执行）关注的是执行的对不对。
Animal a = new Dog();
a.speak();  // 编译时：检查 Animal 有没有 speak() 方法 ✓
            // 运行时：JVM 查虚方法表，发现 a 是 Dog → 执行 Dog.speak()
```

![[Pasted image 20260716111108.png]]

![[Pasted image 20260716111349.png]]
左边1不是的原因，因为重载与返回值无关；左2，在不同的class里面，不构成重载


### 数组在内存堆上的生命周期，着重比较c和java之间的区别
```
例子：
package com.cqu.JavaLearning;  
  
import java.sql.PreparedStatement;  
import java.util.Random;  
  
public class MethodLearning03 {  
    public static void main(String[] args) {  
        Random r = new Random();  
        int[] arr = new int [10];  
        for (int i = 0; i < arr.length; i++) {  
            arr[i] = r.nextInt(100) + 1;  
            System.out.print(arr[i]+" ");  
        }  
  
        int from = 1, to = 5;  
        System.out.println("将arr的数组的从【1,5)进行复制并进行返回");  
  
        int[] copy = copyOfRange(arr, from, to);  
        System.out.println("复制后的数组为：");  
        for (int i = 0; i < copy.length; i++) {  
            System.out.print(copy[i]+" ");  
        }  
    }  
  
    public static int[] copyOfRange(int[] arr, int from, int to){  
        int length = to - from;  
        int[] results = new int[length];  
        for (int i = 0; i < length; i++) {  
            results[i] = arr[from+i];  
        }  
  
        return results;  
    }  
}
```
这里我在写的时候有一个问题，就是results这个数组是在copyOfRange这个方法里面进行申明的，方法调用结束之后，按道理来说此时的results就会被释放啊，因为我记着c好像不行。

实际是这样的，在java中，任何的**对象**都是在堆上面进行申明的，在copyOfRange这个函数中，数组通过new在堆上进行申明，而方法（还有基本数据类型）是在栈里面，当方法结束后，基本数据类型的生命周期消失，数组在堆上面是不被回收的。

那么这个results数组什么时候被回收呢？Java 的堆内存管理靠 **GC（Garbage Collection，垃圾回收器）**，不需要你手动释放，一个对象只要还被至少一个 GC Roots 引用链可达，就活着；一旦不可达，就变成垃圾，等待被回收。

c语言是什么情况呢，对于局部变量都是在栈上面进行分配的，函数结束自动释放，如果你要进行在堆上面进行分配i，你需要：
```
// C 中在函数内返回局部数组地址 —— 危险
int* badFunc() {
    int arr[10];        // 栈上分配
    return arr;         // ❌ 函数结束栈帧销毁，arr 变成悬空指针
}

// C 中要在堆上分配才能安全返回
int* goodFunc() {
    int* arr = malloc(10 * sizeof(int));  // 堆上分配
    return arr;         // ✅ 堆上的数据在函数结束后仍存在
}
```
并且你还需要手动释放调用free()函数

### 当涉及到形参，实参，数组的时候，如果分不清楚话一个堆栈图进行解释
![[Pasted image 20260716120705.png]]

注意：由于从语言层面，你做不到——Java 没有**传引用（pass by reference）**，只有**传值（pass by value）**。你没办法让一个方法拿到调用方栈帧里局部变量的"地址"。所以，现在你可以尝试将传递的形参，或者值包装成一个对象，然后将这个对象传递进去

```
void change(int *p) { *p = 200; }
int a = 100;
change(&a);          // 传 a 的地址

// Java 里你做不到 &a，但可以：
// 创建一个堆上的容器，传容器的引用进去

--------------------------------------------------------------------------------
import java.util.concurrent.atomic.AtomicInteger;

public class Demo {
    public static void main(String[] args) {
        AtomicInteger a = new AtomicInteger(100);
        System.out.println("改变前:" + a.get());
        change(a);
        System.out.println("改变后:" + a.get());
    }

    public static void change(AtomicInteger num) {
        num.set(200);
    }
}
```


### 快捷键自动抽取方法 ctrl+alt+m

### 二维数组的内存图
![[Pasted image 20260716191422.png]]


### 类的注意事项
#### javabean类，用来描述一类事物的类

#### 测试类，编写main方法的类，用来测试javabean类的队形并进行赋值调用

![[Pasted image 20260717113729.png]]

java规定 一个 `.java` 文件里可以有多个类，但 `public` 类只能有一个，且它的名字必须和文件名完全一致。当你写 `new Phone()` 时，编译器需要找到 `Phone` 类的定义。它的查找方式简单粗暴——找文件名叫 `Phone.java`（或编译后的 `Phone.class`）的文件。如果允许 `public class Phone` 写在 `任意文件名.java` 里，编译器需要把所有 .java 文件都扫一遍才能找到它，效率低很多。**注意看修饰符这一章的讲解，了解为什么public不行**


**封装：对象代表什么，就要封装对应的数据，然后并提供对应的行为《感觉在后面的spring里面行为也抽离在一个类中了  》，例如人画圆，draw这个动作行为应该要封装在圆里面，我们假设draw封装在人里面，那么此时人就必须要拥有圆的所有必须数据，一个圆需要什么圆心坐标、半径、颜色、粗细。这些数据都在 `Circle` 类里。此时person就会依赖circle的所有细节，而且这种依赖没有道理——画圆不需要"人"的信息，只需要圆的几何信息。面向对象的设计就是让对象替你做事情，而不是你拿着对象的数据自己算。** 

class Person {
    // ... 一堆其他东西

    // ❌ 画圆既不需要人的名字、年龄、身高，人也不持有圆的数据
    public void draw(Circle c) {
        // 这里只是拿着别人的数据做事
    }
}

**封装的核心原则是两句话：**

1. **一个类把自己的数据和操作这些数据的方法放在一起**（高内聚）。
2. **对外只暴露必要的接口，隐藏内部实现细节**（低耦合）。其他的类使用对应的功能时候，只能调用对应的接口和对应的类，不需要了解对应的全部细节。

### 标准的JavaBean类
![[Pasted image 20260717142127.png]]


### 一个对象创建的内存图
![[Pasted image 20260717143640.png]]

首先程序会从方法区调用一个main方法到栈里面
然后执行student s = new student()这段代码
1. 将student类的class文件加载到方法区里面
2. 栈里面申明一个student s临时变量
3. 堆里面开一个存储student到空间
4. 对这个空间进行初始化
5. 调用构造函数
6. 此时student创建成功，将其地址返回给之前对局部变量s


### this的本质：就是谁调用方法，就取他的地址值
![[Pasted image 20260717144921.png]]

### scanner键盘输入
![[Pasted image 20260717151936.png]]

### String 类： 字符串不可改变，创建之后就不能修改了
![[Pasted image 20260722114739.png]]

![[Pasted image 20260717164458.png]]
![[Pasted image 20260717164601.png]]
当使用直接赋值的时候，jvm首先回去string table里面找是否有对应的串值，如果有则直接给出对应的地址值


### string之间的比较
![[Pasted image 20260717164926.png]]


### string的一个charAt（int index）方法
```
for (int i = 0; i < s1.length(); i++) {  
    System.out.println(s1.charAt(i)); 
}
```

|语言|访问第 i 个字符|说明|
|---|---|---|
|**Java**|`s1.charAt(i)`|String 没有运算符重载，只能用方法|
|**C++**|`s1[i]`|`std::string` 重载了 `operator[]`|
|**C**|`s1[i]`|本质就是 `char[]` 数组，下标直接访问|

### StringBuilder：创建的字符串内容是可以变化的
![[Pasted image 20260717171814.png]]


### StringBuilder or Buffer的equal原码是：
```
public boolean equals(Object obj) {  
    return (this == obj);  
}
```

因此如果我们使用下面的代码与string类型进比较，那么就会出错
```
StringBuilder sb = new StringBuilder();  
sb.append("aaa").append("bbbb");  
if (sb.equals("aaabbbb")){  
    System.out.println("True");  
}else{  
    System.out.println("False");  
}


------------------------------------------
正确的如下：

string的equal原码
public boolean equals(Object anObject) {  
    if (this == anObject) {  
        return true;  
    }  
    return (anObject instanceof String aString)  
            && (!COMPACT_STRINGS || this.coder == aString.coder)  
            && StringLatin1.equals(value, aString.value);  
}

StringBuilder sb = new StringBuilder();  
sb.append("aaa").append("bbbb");  
if (sb.toString().equals("aaabbbb")){  
    System.out.println("True");  
}else{  
    System.out.println("False");  
}
```


### StringJoiner：主要字符串的拼接
![[Pasted image 20260718094312.png]]


### 字符串拼接“+”的底层原理，jdk8之前
![[Pasted image 20260718095203.png]]
	对于s2 这个字符串，它的拼接过程为：
	1. 首先创建一个StringBuilder对象
	2. 然后连续调用两次append(), 将s1和"b"添加进去
	3. 最后调用toString()，通过new String()创建一个String对象，在堆内存里面开辟一个“ab”字符串，然后将地址值传递给s2
	4. 也就是说一个加号需要俩对象的创建

### JDK8+时候字符串拼接“+”的底层原理
![[Pasted image 20260718095345.png]]
**JDK 9+：引入 invokedynamic**
JDK 9 改了底层策略，不再硬编译成 `StringBuilder`，而是生成一条 `invokedynamic` 指令：
```
// 源码
String s2 = s1 + "b";

// JDK 9+ 编译后（简化表示）
String s2 = (String) invokedynamic 拼接策略(s1, "b");
```
运行时 JVM 根据情况选择最优策略，可能的方式有：
1. `StringBuilder`还是用 StringBuilder，兼容老方式|
2. `StringConcat`预计算最终长度，直接 new char[] 填进去，跳过 StringBuilder 的中间层
3. 其他，未来可以再加新策略，不改字节码

### 建议什么时候用+、什么时候用append()
```
// 1. 常量拼接 —— 编译期就优化了，零运行时开销
String url = "https://" + domain + "/api/" + path;

// 2. 少量、固定次数的拼接
String msg = "用户 " + name + " 登录成功";

// 3. 一行能写完的简单拼接
System.out.println("id=" + id + ", name=" + name);



// 循环拼接 —— 必须用 StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(data[i]);
}
String result = sb.toString();
```

![[Pasted image 20260718100716.png]]
![[Pasted image 20260718101024.png]]
这里有个关键区别——**编译期常量折叠（Constant Folding）**。

`"a"+"b"+"c"` 和之前讨论的 `s1 + "b"` 看似都是拼接，但在编译器看来完全不同：

|写法|编译器处理|
|---|---|
|`"a"+"b"+"c"`|三个都是**字面量常量**，编译时直接算出 `"abc"`|
|`s1 + "b"`|`s1` 是变量，编译时不知道 `s1` 的值，运行时才拼|

所以你的代码编译后反编译看，实际等价于：

```
String s1 = "abc";
String s2 = "abc";    // 编译期已经算好了
System.out.println(s1 == s2);
```

两个都是 `"abc"`，指向**字符串常量池中的同一个对象**，所以 `s1 == s2` 输出 **`true`**。

---

**对比前面另一个例子：**

```
String s1 = "a";
String s2 = s1 + "b";     // s1 是变量，运行时拼 → false

String s3 = "a" + "b";    // 两个常量，编译期折叠 → true
```

所以这个例子的知识点就是：**全是字面量的字符串拼接，编译器会在编译期直接帮你算好，不进 StringBuilder，不进运行时。**


### 字符串原理小结：
字符串拼接的原理：
1. 如果没有变量参与拼接，字符串都是相加的，那么编译器会在编译时期复用串池里面的字符串进行直接进行拼接，不会创造新的对象。 编译之后就是凭借之后的结果，这个结果是在串池里面的。**编译期常量折叠
2. 如果有变量参与，这时候就有两种情况，一个是jdk8之前，+拼接等于 一个StringBuilder调用两次append，然后利用toString 函数new一个对象，那么此时你得到的字符串数据是在堆内存里面；jdk8以后，编译器首先会进行预估invokedynamic，可以采用老方法利用stringbuilder，可以预估大小值，然后利用char\[], 进行存储数据然后凭借赋值, 最后通过一个new String进行生产，那么也就是说还是在堆内存里面。
3. 串池里面数据，它只存编译期就已经知道的那些字符串常量。除非你 String s2 = new String("def").intern(); // 运行时手动把 "def" 放入串池

![[Pasted image 20260718102750.png]]

### ArrayList\<Ojection obj> 里面只能加入对象，基本数据类型不行


![[Pasted image 20260721112441.png]]

工具类常用private声明构造方法，因为没有必要生成这个对象，我们常用static构造静态方法，到时候直接用类名.静态方法

![[Pasted image 20260721113550.png]]

### main函数里面的args是什么意思
**本质：** 命令行上按空格分割的原始字符串数组，约等于 Python 的 `sys.argv[1:]`
java MyApp --lr 0.001 --batch_size 32
其实你看到这个就明白了，args\[]这个字符串数组读取的就是"--lr 0.001 --batch_size 32"

### 继承的相关学习
![[Pasted image 20260721160225.png]]
**构造函数**肯定不能继承，因为如果继承的话，那么一个子类生成，那么它的父类也要创造，这样就麻烦了，创建一个类要连续生产很多的父类，内存会容量会变大

**成员变量**，private不能使用，但是你可以更具get来获得。为什么私有的成员变量可以被继承，因为对于我们创建一个对象来说，首先会从方法区加载对应的字节码文件（包括父类），然后在堆内存里面开辟一个空间，此时堆内存就会存储这个父类的成员变量了。

现在我们来假设如果在字节码加载时跳过私有字段，看一个例子：
```
class Animal {
    private int age;

    public void grow() {
        age++;          // 父类自己的方法操作自己的私有字段
    }

    public int getAge() {
        return age;     // 同样是父类方法访问私有字段
    }
}

class Dog extends Animal {
    // 子类无法直接访问 age
    // 但子类继承了 grow() 和 getAge()
}

[Dog 对象] ─┬─ Animal 部分: [age: int, ...]
             └─ Dog 部分: [...]
             
此时对于这个getAge这个方法就访问不到age这个字段了，此时内存就会报错
```

| 层面             | 控制什么                | private 是否影响            |
| -------------- | ------------------- | ----------------------- |
| **编译期 — 访问控制** | 能否在源代码里写出 `obj.age` | 是，编译器直接报错               |
| **运行时 — 内存布局** | 对象里有没有这个字段          | **否**，JVM 按完整的类层次结构分配空间 |
Java 选择了一个更朴素的路线：**对象里该有什么就有什么，能不能碰是另一回事**
![[Pasted image 20260721161257.png]]

![[Pasted image 20260721163202.png]]
### 掌握第四点就行了
![[Pasted image 20260722095056.png]]


### 继承关系中，子类的构造方法首先要调用父类的无参构造方法
![[Pasted image 20260722100139.png]]


### 多态的应用场景
![[Pasted image 20260722101429.png]]
![[Pasted image 20260722102200.png]]

![[Pasted image 20260722103423.png]]

```
class Animal {
    String tag = "Animal";
    void shout() {
        System.out.println("Animal shout");
    }
}

class Dog extends Animal {
    String tag = "Dog";   // 这叫 shadow（遮蔽），不叫 override
    void shout() {
        System.out.println("Dog shout");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal a = new Dog();
        System.out.println(a.tag);   // → "Animal"
        a.shout();                    // → "Dog shout"
    }
}
```

对于一个a来说，成员变量访问的是父类的属性，而对于成员方法则是访问的是子类的方法。
对于成员变量来说，实际存放的地方是在堆里面的，当编译器访问a.tag的时候，是根据 `a` 的声明类型（`Animal`）算出偏移量，读取那一块内存。

而对于方法来说调用a.shout()时候，JVM走的是vtable虚方法表，从对象头的 Klass Pointer 找到 `Dog` 的类元数据，再从虚方法表里查到 `shout()` 的真正入口。

### 权限修饰符的分类
![[Pasted image 20260722115116.png]]

### 了解构造代码块即可
![[Pasted image 20260722120148.png]]


### 静态代码块
```
static{}
在类中写就行了，执行的时机为：类加载的时候，仅执行一次
```

### interface 里面的default字段，是为实现接口升级时，如果加入一个abstract会导致使用它的子类也被强制所有重新重构。
![[Pasted image 20260722154406.png]]



### interface里面的static方法
![[Pasted image 20260722154759.png]]

```
package com.cqu.Interface01;  
  
public interface Inter {  
  
    public void method();  
  
    public static void speakEnglish() {  
        System.out.println("speak english");  
    }  
}


package com.cqu.Interface01;  
  
public class InterImpl implements Inter {  
  
    @Override  
    public void method() {  
  
    }  
}

package com.cqu.Interface01;  
  
public class Test {  
    public static void main(String[] args) {  
        InterImpl inter =  new InterImpl();  
        inter.method();  
  
        Inter.speakEnglish();  
        //inter.speakEnglish(); 报错  
    }  
}

```

为什么不同通过对象调用接口的静态方法呢，以及为什么不能重写呢
调用，是对象通过JVM查询虚方法表的方法进行调用，由于虚方法表没有static方法，因此不行
重写的要求是对虚方法表里面的方法进行重新书写，对于private、final、static的方法不属于虚方法表。

static 方法属于"类型"自己，不属于实例，永远不参与多态。
### 规矩一：静态方法统一用 `类名.调用`

```
// ✅ 正确（所有人都会这样写）
Utils.printHello();
Inter.speakEnglish();

// ❌ 虽然能跑，但 IDE 会标黄警告，代码审查时会被骂
Utils u = new Utils();
u.printHello();          // "不要通过实例访问静态成员"
```

**不要用对象调静态方法**，这是开发规范，不是技术限制。

---

### 规矩二：静态方法不存在重写

```
class Fu {
    static void hello() { ... }
}
class Zi extends Fu {
    static void hello() { ... }  // 这叫"隐藏"，不叫重写
}

Fu.hello();   // 调 Fu 的
Zi.hello();   // 调 Zi 的
// 没有多态，没有覆盖，各调各的
```

实际开发中**几乎不会**在子类里写一个和父类同名的静态方法，因为没有意义，还容易让人迷惑。

---

### 规矩三：接口的静态方法只能用接口名调

```
Inter.speakEnglish();        // ✅
// InterImpl.speakEnglish(); // ❌ 编译错误
// obj.speakEnglish();       // ❌ 编译错误
```

这是最严格的——接口的静态方法就是为了给接口自己用的工具方法，实现类连调用资格都没有。

---

### 总结

> **面试问你细节**：把虚方法表、invokestatic 搬出来  
> **实际写代码**：记住三条规矩就行——静态方法永远用类名调、没有重写这回事、接口的 static 只能用接口名调

![[Pasted image 20260722161236.png]]

![[Pasted image 20260722161447.png]]

![[Pasted image 20260722161829.png]]

### 内部类
![[Pasted image 20260723095744.png]]

为什么外部类不能够访问内部类的成员呢，首先就是c.show()，这个方法传递了一个this，this存储了当前这个类Car的地址值，此时你不能通过this进行访问。同时创建外部类的时候，不会创建对应的内部类对象，因此你是拿不到内部类的值


### 为什么内部类能够访问外部类的成员变量，因为内部类存放类一个外部类.this 的地址值！！！
![[Pasted image 20260723102711.png]]


### 关于extend，多态的我的理解
首先，继承我们的父类肯定有含有一个共有的属性和方法，对于这些方法来说，我们一般在子类是不会在重新写一遍的，我们只需要在子类上重新加一些新的方法和属性就行了。

**如果我们这时候我们还要手贱，去写一遍**，对于多态而言， 父类 s = new 子类(参数)；那么，s.参数（父类和子类共有的参数），我们想要的是子类的参数，但是由于编译器是通过.访问的是父类的内存空间，那么我们访问的是父类的参数，也就是参数访问的左边的；
对于方法而言，由于重写了一遍，虚方法表里面的父类方法被子类给覆盖了，所以我们还是访问的子类的方法（包含从父类继承的方法）。

避免出错的话，不要随意的重构、重写（abstract除外）


### Object里面的toString、equals函数
如果想要输出的不是地址值，那么重写这个函数就行了

### 克隆：浅克隆和深克隆，oject的clone为浅克隆. 重点看一下代码怎么写的

### 浅克隆
![[Pasted image 20260724100347.png]]

### 深克隆
![[Pasted image 20260724100326.png]]
![[Pasted image 20260724100258.png]]



### BigInteger和BigDecimal的一些细节
![[Pasted image 20260724110327.png]]

```
public static BigDecimal valueOf(double val) {
    return new BigDecimal(Double.toString(val));
}
```

`Double.toString(0.989)` 输出的是 `"0.989"` —— Java 的 `Double.toString` 有去舍逻辑：它只生成能唯一区分这个 double 值的最少小数位数，不会把二进制尾数里的微小误差暴露出来。


### 正则表达式
![[Pasted image 20260724143332.png]]


### 爬虫配合正则表达式
```
package com.cqu.regex;  
  
import java.util.regex.Matcher;  
import java.util.regex.Pattern;  
  
public class RegexDemo {  
    public static void main(String[] args) {  
        String str = "ithisjava1isa code\n" +  
                "learningjavaforyears\n" +  
                "thejavavmrunseverywhere\n" +  
                "effectivejava12itemone";  
        Pattern p = Pattern.compile("java\\d{0,2}");  // 先编译
        Matcher m = p.matcher(str);  // 创建matcher对象
  
        while (m.find()) {  // 寻找
            System.out.println(m.group()); // 找到并分组  
        }  
    }  
}
```

### SimpleDateFormat
你就学会两个就行了，解析和格式化这两个函数，字符串无法比较，你把转换为date类型，然后通过get函数得到毫秒值就行了
![[Pasted image 20260724154438.png]]


### Calendar
你在外面写 `Calendar cal = Calendar.getInstance();`，**编译时类型**（左边）是抽象的 `Calendar`，但**运行时类型**（右边实际 new 出来的）是 `GregorianCalendar`。
```
public static Calendar getInstance() {
    // 实际返回的是 GregorianCalendar，它是 Calendar 的子类
    return new GregorianCalendar(TimeZone.getDefault(), Locale.getDefault());
}
```

![[Pasted image 20260724160233.png]]

![[Pasted image 20260724160955.png]]


### 包装类
![[Pasted image 20260724161553.png]]

在Integer中 -128-127里面的数据，java都会实现的创建对应的对象，因此我们使用的时候就直接应用就行了

![[Pasted image 20260724161843.png]]

![[Pasted image 20260724161901.png]]

### 基本的查找方法
![[Pasted image 20260726104114.png]]


### Array类的常用用法
![[Pasted image 20260726152510.png]]

```
package com.cqu.arrayLearning;  
  
import java.lang.reflect.Array;  
import java.util.Arrays;  
import java.util.Comparator;  
  
public class ArrayLearning {  
    public static void main(String[] args) {  
        //第一个知识点：对于int 类型的数组来说，Arrays采用的双轴快排算法  
        //第二个知识点，为什么java的Arrays.sort要区分Integer和int呢,主要原因就是出现在了comparator<>上面  
        //需要使用泛型,但是int作为基本数据类型是不支持的,因此这样不可以的.  
  
        int[] arr = {1,65,54,2,6,8,0,4,23};  
        Arrays.sort(arr);  
        System.out.println(Arrays.toString(arr));  
  
  
        //第三个知识点,comparator的排序采用的是插入算法加二分查找;  
        /*        * o1代表的是无序序列,o2代表的是有序序列,  
        * 算法的整个过程是,假设当前o1所指的元素为A,  
        * 把A往有序序列 里面插入,在插入的时候利用二分查找确定A元素的插入点  
        * 那么此时就会和O2的数据进行比较,比较的规则就是compare  
        * 如果说返回值是负数,则拿着A继续和前面的数据比较.(插入的元素是小的,放在前面)  
        * 如果是正数或者0,则继续和A后面的数据进行比较.(插入的元素是大的,或者是一样的,放在后面)  
        * */        Integer[] arr1 = {1,65,54,2,6,8,0,4,23};  
        Arrays.sort(arr1, new Comparator<Integer>() {  
            @Override  
            public int compare(Integer o1, Integer o2) {  
                return o1 - o2;  
            }  
        });  
  
        System.out.println(Arrays.toString(arr1));  
  
  
        Integer[] arr2 = {1,65,54,2,6,8,0,4,23};  
        Arrays.sort(arr2, new Comparator<Integer>() {  
            @Override  
            public int compare(Integer o1, Integer o2) {  
                return o2 - o1;  
            }  
        });  
        System.out.println(Arrays.toString(arr2));  
    }  
}
```


### Lambda表达式
![[Pasted image 20260726154951.png]]
![[Pasted image 20260726154837.png]]

### ArrayList的删除常见问题
`ArrayList.remove(int index)` 底层是数组，删除不是简单的"挖空"，而是**后面的元素整体前移一位**。

源码大致如下：

```
public E remove(int index) {
    E oldValue = elementData[index];          // 1. 记住要删除的元素
    
    int numMoved = size - index - 1;          // 2. 需要前移的元素个数
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, // 从 index+1 开始
                         elementData, index,    // 往 index 处复制
                         numMoved);            // 复制 numMoved 个
    elementData[--size] = null;              // 3. 尾部置空，size-1
    return oldValue;
}
```

用你的兔子例子直观感受。假设 `rbsCannotProduce` 当前为 `[兔A, 兔B, 兔C]`，调用 `remove(0)` 删除兔A：

```
删除前: [兔A, 兔B, 兔C, null, null, ...]
           ↑
         index=0

System.arraycopy 执行:
  把 elementData[1]→复制到→elementData[0]
  把 elementData[2]→复制到→elementData[1]
  移动 2 个元素

删除后: [兔B, 兔C, 兔C, null, null, ...]  
                      ↑ 这个就是原来兔C的旧位置，被置 null
         [兔B, 兔C, null, null, null, ...]
          size=2
```

所以 `remove(0)` 之后：

- 原来在索引 1 的兔B → 现在在索引 0
- 原来在索引 2 的兔C → 现在在索引 1

如果你的循环紧接着 `j++`（j 从 0 变 1），你处理的就是现在的索引 1（兔C），兔B 被完美跳过——它已经滑到了索引 0，而你再也回不去了。

也就是说我们**要倒着删除。**

**正常来说对于删除的操作，我还是用迭代器进行删除**

### ArrayList add方法的底层原理
1. 首先创建一个空数组的时候，size=0；
2. 当添加第一个元素的时候，此时数组扩容到size=10；
3. 当超过10的时候，此时add又要扩1.5倍，也就是15；
4. 如果要添加很多元素的时候，此此时需要扩容，那么我们就要更具需要的大小进行扩容

![[Pasted image 20260727145223.png]]

这里我想强调的是，利用list进行remove元素的时候，list的iteration有一个叫int expectedModCount = modCount; 使用remove或者add，会导致modCount+1，而由于expectedModeCount在创建时候就已经确定了，此时两者不相等
final void checkForComodification() {  
    if (modCount != expectedModCount)  
        throw new ConcurrentModificationException();  
}

### LinkList的功能
![[Pasted image 20260727143805.png]]
### 集合体系
![[Pasted image 20260727104338.png]]

注意：这里的有序和无序是指的，存和取是一样的顺序，而不是递增，递减的顺序

![[Pasted image 20260727104442.png]]

remove这里一定主要注意，如果要for循环删除判断每一个元素是否能够删除，一定要倒着删除。以及由于set集合没有索引，而collection是他们的父类，所以collection在设计的时候，remove不会针对索引进行删除

**contains的一个小细节**，contains的原码是通过Object.equals进行比较的，因此如果我们自定义了一个类的时候，我们一定要重现这个类的equals方法来进行，否则就比较多是地址值

在用迭代器进行遍历的时候是不能够用集合的方法进行删除的，只能用Iterator进行删除
![[Pasted image 20260727120124.png]]
![[Pasted image 20260727122206.png]]

![[Pasted image 20260727122355.png]]
这里使用增强for也是不行的，因为这里蕴含着用的也是迭代器
![[Pasted image 20260727121834.png]]

最主要的就是删除和添加元素，请你使用迭代器，注意普通的迭代器是没有对应的add的。
 **用 `list.remove()` 的情况**

1. **不在迭代中用**——就是看一眼索引，直接删一次：
    
    ```
    list.remove(2);
    list.remove("aaa");
    ```
    
2. **用传统 for 循环但是手动修正索引**——删完之后 `i--` 让指针退一步：
    
    ```
    for (int i = 0; i < list.size(); i++) {
        if (条件) {
            list.remove(i);
            i--;           // 退回去，因为下一个元素滑到当前位置了
        }
    }
    ```
    
3. **倒着遍历**——从后往前删，后面的索引不会被前面的删除影响，也不用管 i--：
    
    ```
    for (int i = list.size() - 1; i >= 0; i--) {
        if (条件) list.remove(i);
    }
    ```
    
4. **批量操作**——不涉及遍历，语义清晰：
    
    ```
    list.removeAll(某些集合);
    list.retainAll(某些集合);
    ```
    

用 `iterator.remove()` 的情况
1. **用 Iterator 或 for-each 遍历时做条件删除**——这是 for-each 里唯一安全的写法：
    
    ```
    Iterator<Student> it = stus.iterator();
    while (it.hasNext()) {
        if (条件) it.remove();
    }
    ```
    
2. **需要注意**：for-each 底下就是 Iterator，所以下面这种写法会抛异常：
    
    ```
    for (Student s : stus) {
        if (条件) stus.remove(s);   // ❌ ConcurrentModificationException
    }
    ```
    

但最推荐的现代写法是 `removeIf`
Java 8 之后，99% 的遍历删除都可以被这一行替代：

```
stus.removeIf(stu -> stu.equals(stu2));
```

底层帮你封装好了 iterator 的 remove 逻辑，你不需要操心索引、指针、版本号这些事：

```
// ArrayList 的 removeIf 大致实现：
public boolean removeIf(Predicate<? super E> filter) {
    final ArrayList<E> list = this;
    final int size = this.size;
    BitSet removeSet = new BitSet(size);
    // 先标记要删的、再批量删除——一次结构变更
    ...
}
```

它比循环里挨个 `remove()` 还更高效，因为只触发一次结构修正。

---

 总结一表

| 场景                   | 用什么                       |
| -------------------- | ------------------------- |
| 不遍历，删一个              | `list.remove(index/obj)`  |
| 倒着遍历删除               | `list.remove(i)` + 倒序 for |
| for-each 遍历中条件删除     | `iterator.remove()`       |
| 批量条件删除（推荐）           | `list.removeIf(...)`      |
| 传统 for + 索引遍历 + 条件删除 | `list.remove(i); i--;`    |
|                      |                           |

### 泛型，注意java是一个伪泛型
![[Pasted image 20260727155545.png]]
在编译的时候会检查当前的泛型是否符合要求，但是在真正的.class文件里面，其实存储的是object对象，在遍历的时候，会自动将其强转为对应的类型。

这也解释了为什么不能基本数据类型创建对应的list对象，因为基本数据类型不能转换为oject对象。

![[Pasted image 20260727161004.png]]

### 泛型的通配符
![[Pasted image 20260727161523.png]]

泛型不具备继承性：如果说有三个类型的Ye、Fu、Zi他们以此继承，当使用泛型的时候，也就是说类似于ArrayLIst\<Ye>、ArrayLIst\<Fu>、ArrayLIst\<ZI>这三个类型的此时是不具有继承的类型的，也就是说他们三者不相关

但是对于他们存储的数据类型Ye、Fu、Zi任然有对应的继承关系

![[Pasted image 20260727162359.png]]

### 平衡二叉树的旋转机制
这里的二叉树其实指代的是二叉排序树，由于二叉排序树它只要求任意左子树要小于右子树，所以这就导致了树的形状不一，很可能会出现一种情况就是，左边的子树高度为0（也就是没有任何节点），而右边的子树很长，这时候树就退化成为了一个链表，此时的查询效率就大大降低了，因此，为了提高查询效率，规定了平衡二叉树，任意左右子树的高度之差不能超过1.而如何实现这种机制，下面就要讲到旋转机制。
![[Pasted image 20260728110257.png]]
#### **平衡因子**
**定义**：某节点的左子树与右子树的高度(深度)差即为该节点的平衡因子（BF,Balance Factor），平衡二叉树中不存在平衡因子大于 1 的节点。在一棵平衡二叉树中，节点的平衡因子只能取 0 、1 或者 -1 ，分别对应着左右子树等高，左子树比较高，右子树比较高。
![[Pasted image 20260728112635.png]]

#### **左旋**
![[Pasted image 20260728112723.png]]
此时的失衡点为**66这个节点**，那么此时我们需要进行一次左旋处理，处理结果如下。（1）失衡节点的右子树代替此节点的位置（2）右子树的左子树变成失衡节点的右子树（3）失衡节点本身变成右子树的左子树
![[Pasted image 20260728112832.png]]

#### **右旋**
右旋的过程同理：
![[Pasted image 20260728113052.png]]
结果如下：
（1）失衡节点的左子树取代当前的位置（2）左子树的右子树为失衡节点的左子树（3）失衡节点本身变成左子树的右子树
![[Pasted image 20260728113122.png]]

#### 什么时候旋转
（1）首先判断你现在需要插入的位置
（2）找到失衡节点的位置
（3）判断是左左（一次右旋）；右右（一次左旋）；左右（一次左旋+一次右旋）；右左（一次右旋+一次左旋）

![[Pasted image 20260728113731.png]]
我们可以看到12为插入的节点，失衡点为7，12 位于 7 的右子树的右子树，属于右右的情况，因此做一次左旋就行了。

![[Pasted image 20260728114046.png]]
**我们可以看到8为插入的节点，失衡点为7，8位于失衡点的右子树的左子树，先对失衡点的右子树（蓝色的部分）进行一次右旋，然后对红色的节点进行左旋即可。**


若 A 的左孩子节点 B 的右子树 E 插入节点 F ，导致节点 A 失衡，如图：
![[Pasted image 20260728114213.png]]
A 的平衡因子为 2 ，若仍按照右旋调整，则变化后的图形为这样：
![[Pasted image 20260728114240.png]]
经过右旋调整发现，调整后树仍然失衡，说明这种情况单纯的进行右旋操作不能使树重新平衡。那么这种插入方式需要执行两步操作，使得旋转之后为 **原来根结点的左孩子的右孩子作为新的根节点** 。

（1）对失衡节点 A 的左孩子 B 进行左旋操作，即上述 RR 情形操作。 （2）对失衡节点 A 做右旋操作，即上述 LL 情形操作。

![[Pasted image 20260728114533.png]]
![[Pasted image 20260728114539.png]]
也就是说，经过这两步操作，使得 **原来根节点的左孩子的右孩子 E 节点成为了新的根节点**。

### 红黑树
红黑树是一个特殊的二叉查找树。高度不平衡，拥有特殊的红黑规则。
![[Pasted image 20260728115423.png]]
![[Pasted image 20260728115938.png]]
   ![[Pasted image 20260728120719.png]]
**注意一定是要注意当前节点判断**


### Set的性质：无序，不重复，没有索引
![[Pasted image 20260728143458.png]]

### HashSet集合
![[Pasted image 20260728143801.png]]
![[Pasted image 20260728144406.png]]

#### HashSet JDK8以前底层原理
![[Pasted image 20260728144909.png]]


1. 当加入的数据长度大于等于16 × 0.75 =12 的时候，此时数组就会扩容成两倍，变成32，依次类推
2. 位置 = （数组的长度-1） & 哈希值
3. 对于哈希值一样的数据，首先会通过equal判断是否相等，如果不相等，则形成链表；如果相等则不存入
	1. 这里形成链表就要区别了，老版本jdk8以前是新元素存入数组，而老元素挂在下面
	2. jdk8以后，新元素直接过载到老元素下面，同时当这个链表的长度大于8且数组的长度大于等于64的时候，则会转换为红黑树提高查询修改的效率
![[Pasted image 20260728145340.png]]
因此如果我们的集合里面是对象的话，我们必须要重写equal和hashcode函数

### LinkedHashSet底层原理：有序、不重复、无索引
![[Pasted image 20260728150253.png]]

### TreeSet的特点及其排序
![[Pasted image 20260728150749.png]]
![[Pasted image 20260728153152.png]]
由于TreeSet的底层是红黑树，因此，我们没有必要写hashCode和equal函数，但是我们主要实现的是：
![[Pasted image 20260728152621.png]]
这个接口，其实跟之前的ArrayList.sort方法重写一样
![[Pasted image 20260728152859.png]]

对于TreeSet的添加规则，他是根据红黑树进行存储的，因此add也要遵守红黑规则
![[Pasted image 20260728153015.png]]

![[Pasted image 20260728155259.png]]

### 使用场景
反正就一句话，如果允许重复，优先用ArrayList；不允许重复，优先利用HashSet；然后如果要排序就用TreeSet，其他的很少用。
![[Pasted image 20260728155333.png]]

另外一个就是，算法面试的时候，如果你想要在O（1）的时候找到想要的数据，则建议用hashset

|类型|contains 复杂度|
|---|---|
|`HashSet` / `LinkedHashSet`|O(1) 平均|
|`EnumSet`|O(1)|
|`TreeSet` / `ConcurrentSkipListSet`|O(log n)|
|`ArrayList` / `LinkedList` / 所有 List|O(n)|
|`PriorityQueue` / `ArrayDeque`|O(n)|
### 双列集合
特点：key-value，key不能重复，value能够重复，在java中，我们一般叫做entry对象
![[Pasted image 20260728162346.png]]

### HashMap的特点
![[Pasted image 20260729095727.png]]


### LinkedHashMap
![[Pasted image 20260729115918.png]]

### TreeMap
![[Pasted image 20260729120204.png]]

**TreeMap的一些小细节**，当自己创建一些自定定义对象的时候，一定要实现comparable接口或者传递比较器对象，否则会报错，例如
```
package com.cqu.hashmaplearning;  
  
public class Student2 {  
    private String name;  
    private int age;  
  
  
    public Student2() {  
    }  
  
    public Student2(String name, int age) {  
        this.name = name;  
        this.age = age;  
    }  
  
    /**  
     * 获取     * @return name  
     */    public String getName() {  
        return name;  
    }  
  
    /**  
     * 设置     * @param name  
     */  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    /**  
     * 获取     * @return age  
     */    public int getAge() {  
        return age;  
    }  
  
    /**  
     * 设置     * @param age  
     */  
    public void setAge(int age) {  
        this.age = age;  
    }  
  
    public String toString() {  
        return "Student2{name = " + name + ", age = " + age + "}";  
    }  
  
  
}


package com.cqu.hashmaplearning;  
  
import java.util.TreeMap;  
  
public class TreeMapDemo04 {  
    public static void main(String[] args) {  
        Student2 s1 = new Student2("zhangsan", 18);  
        Student2 s2 = new Student2("lisi", 20);  
        Student2 s3 = new Student2("wangwu", 19);  
  
        TreeMap<Student2, String> tm = new TreeMap<>();  
  
        tm.put(s1, "北京");  
        tm.put(s2, "上海");  
        tm.put(s3, "广州");  
  
        System.out.println(tm);  
    }  
}

包括原因：
Exception in thread "main" java.lang.ClassCastException: class com.cqu.hashmaplearning.Student2 cannot be cast to class java.lang.Comparable (com.cqu.hashmaplearning.Student2 is in unnamed module of loader 'app'; java.lang.Comparable is in module java.base of loader 'bootstrap')
	at java.base/java.util.TreeMap.compare(TreeMap.java:1604)
	at java.base/java.util.TreeMap.addEntryToEmptyMap(TreeMap.java:811)
	at java.base/java.util.TreeMap.put(TreeMap.java:820)
	at java.base/java.util.TreeMap.put(TreeMap.java:569)
	at com.cqu.hashmaplearning.TreeMapDemo04.main(TreeMapDemo04.java:13)

```

我们来查看一下原码：
在TreeMap调用put添加元素的时候，
![[Pasted image 20260729142303.png]]