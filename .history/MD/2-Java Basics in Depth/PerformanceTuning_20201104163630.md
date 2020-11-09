- [JVM](#jvm)
  - [实现原理](#实现原理)
  - [JVM内存模型](#jvm内存模型)
  - [Java类文件结构](#java类文件结构)
  - [Java虚拟机类加载机制与类加载器](#java虚拟机类加载机制与类加载器)
    - [类加载方式](#类加载方式)
    - [类加载的动态性体现:](#类加载的动态性体现)
    - [类加载过程](#类加载过程)
  - [运行时数据区：深入理解堆/栈/元空间](#运行时数据区深入理解堆栈元空间)
    - [方法区](#方法区)
    - [堆内存](#堆内存)
    - [程序计数器](#程序计数器)
    - [虚拟机栈](#虚拟机栈)
    - [本地方法栈](#本地方法栈)
    - [元空间](#元空间)
  - [垃圾回收机制](#垃圾回收机制)
    - [CMS(Concurrent Mark Sweep)](#cmsconcurrent-mark-sweep)
    - [G1](#g1)
    - [ZGC](#zgc)
    - [Shenandoah](#shenandoah)
    - [Epsilon](#epsilon)
  - [Java字节码执行引擎](#java字节码执行引擎)
  - [JKD性能监控于故障处理](#jkd性能监控于故障处理)
  - [JVM服务器实战调优](#jvm服务器实战调优)
    - [GCView](#gcview)
    - [Arthas](#arthas)
  - [JVM面试题解析](#jvm面试题解析)
- [MySQL](#mysql)
  - [MySQL优化基础](#mysql优化基础)
  - [多版本并发控制MVCC](#多版本并发控制mvcc)
  - [应用程序性能剖析](#应用程序性能剖析)
  - [慢查询日志剖析](#慢查询日志剖析)
  - [数据库优化与索引](#数据库优化与索引)
  - [BTree索引/Hash索引/聚簇索引/非聚簇索引](#btree索引hash索引聚簇索引非聚簇索引)
  - [索引的选择和表的维护](#索引的选择和表的维护)
  - [深入索引原理解析](#深入索引原理解析)
  - [高效索引策略与SQL查询优化](#高效索引策略与sql查询优化)
  - [SQL优化查询器](#sql优化查询器)
- [Tomcat](#tomcat)
  - [Tomcat概述](#tomcat概述)
  - [Web引用通信机制](#web引用通信机制)
  - [Tomcat重要核心组件](#tomcat重要核心组件)
  - [Tomcat类加载器](#tomcat类加载器)
  - [Tomcat内部原理与线程模型](#tomcat内部原理与线程模型)
  - [HTTP协议和BIO/NIO](#http协议和bionio)
  - [Tomcat高可用集群架构方案](#tomcat高可用集群架构方案)
  - [Tomcat高可用集群架构实现](#tomcat高可用集群架构实现)
  - [Tomcat性能调用实战](#tomcat性能调用实战)


# JVM
JVM是Java Virtual Machine（Java虚拟机）的缩写，JVM是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。Java虚拟机包括一套字节码指令集、一组寄存器、一个栈、一个垃圾回收堆和一个存储方法域。 JVM屏蔽了与具体操作系统平台相关的信息，使Java程序只需生成在Java虚拟机上运行的目标代码（字节码）,就可以在多种平台上不加修改地运行。JVM在执行字节码时，实际上最终还是把字节码解释成具体平台上的机器指令执行。这就是Java的能够“一次编译，到处运行”的原因。

## 实现原理
1. jvm是java的核心和基础，在java编译器和os平台之间的虚拟处理器，可在上面执行字节码程序。
2. java编译器只要面向jvm，生成jvm能理解的字节码文件。java源文件经编译成字节码程序，通过jvm将每条指令翻译成不同的机器码，通过特定平台运行。

![img](./img/1587882-20190308111205469-1550734334.png)

## JVM内存模型
JVM = 类加载器(classloader) + 执行引擎(execution engine) + 运行时数据区域(runtime data area)

![img](./img/1587882-20190308115822736-2106039872.png)

如图所示jvm内存划分为五部分
- 方法区（Method Area）  
  方法区存放了要加载的类的信息（如类名、修饰符等）、静态变量、构造函数、final定义的常量、类中的字段和方法等信息。方法区是全局共享的，在一定条件下也会被GC。当方法区超过它允许的大小时，就会抛出OutOfMemory：PermGen Space异常。
- 堆区（Heap）  
  堆区是GC最频繁的，也是理解GC机制最重要的区域。堆区由所有线程共享，在虚拟机启动时创建。堆区主要用于存放对象实例及数组，所有new出来的对象都存储在该区域。
- 虚拟机栈（VM Stack）  
  虚拟机栈占用的是操作系统内存，每个线程对应一个虚拟机栈，它是线程私有的，生命周期和线程一样，每个方法被执行时产生一个栈帧（Statck Frame），栈帧用于存储局部变量表、动态链接、操作数和方法出口等信息，当方法被调用时，栈帧入栈，当方法调用结束时，栈帧出栈。
- 本地方法栈（Native Method Stack）  
  本地方法栈用于支持native方法的执行，存储了每个native方法的执行状态。本地方法栈和虚拟机栈他们的运行机制一致，唯一的区别是，虚拟机栈执行Java方法，本地方法栈执行native方法。在很多虚拟机中（如Sun的JDK默认的HotSpot虚拟机），会将虚拟机栈和本地方法栈一起使用。
- 程序计数器（Program Counter Register）  
  程序计数器是一个很小的内存区域，不在RAM上，而是直接划分在CPU上，程序猿无法操作它，它的作用是：JVM在解释字节码（.class）文件时，存储当前线程执行的字节码行号，只是一种概念模型，各种JVM所采用的方式不一样。字节码解释器工作时，就是通过改变程序计数器的值来取下一条要执行的指令，分支、循环、跳转等基础功能都是依赖此技术区完成的。

## Java类文件结构
Class文件是一组以8位字节为基础单位的二进制流，各项数据项目严格按照顺序紧凑地排列在Class文件之中，中间没有添加任何分隔符，如果是超过8位字节以上空间的数据项，则会按照高位在前的方式（Big-Endian）分割成若干个8位字节进行存储。

Class文件中包含了Java虚拟机指令集和符号表以及若干其他辅助信息。

Class文件格式只有两种数据类型：无符号数和表。

无符号数属于基本的数据类型，以u1,u2,u4,u8来分别代表1个字节，2个字节，4个字节和8个字节的无符号数；可用来描述数字，索引引用，数量值或者按照UTF-8编码构成的字符串值。

表是由多个无符号数或者其他表作为数据项构成的复合数据类型，所有表都习惯性地以“_info”结尾。表用于描述由层次关系的复合结构的数据，整个Class文件本质上就是一张表。

![img](./img/940623-20170402225302461-264984043.png)

## Java虚拟机类加载机制与类加载器
Java源代码被编译为字节码文件后，需要加载进内存才能在程序中被使用。程序启动时并不会一次性加载程序要用的所有class文件，而是根据程序需要，通过Java的类加载机制（ClassLoader）动态加载某个class文件到内存当中。ClassLoader就是用来在运行时加载字节码文件进内存的，加载的过程是线程安全的。

> 如何保证类加载时线程安全：同步代码块synchronized+ConcurrentHashMap 用于映射类名+锁

### 类加载方式
1. 隐式装载， 程序在运行过程中当碰到通过new 等方式生成对象时，隐式调用类装载器加载对应的类到jvm中。 
2. 显式装载， 通过class.forname()等方法，显式加载需要的类

### 类加载的动态性体现:
一个应用程序总是由n多个类组成，Java程序启动时，并不是一次把所有的类全部加载后再运行，它总是先把保证程序运行的基础类一次性加载到jvm中，其它类等到jvm用到的时候再加载，这样的好处是节省了内存的开销，因为java最早就是为嵌入式系统而设计的，内存宝贵，这是一种可以理解的机制，而用到时再加载这也是java动态性的一种体现

### 类加载过程
1. 对字节码文件（class文件）进行加载
2. 连接，将原始的类定义信息转入JVM运行（分配内存）
    - 验证：JVM 需要核验字节信息是符合 Java 虚拟机规范的，否则就被认为是 VerifyError，这样就防止了恶意信息或者不合规信息危害 JVM 的运行，验证阶段有可能触发更多 class 的加载。
    - 准备：创建类或者接口中的静态变量，并初始化静态变量的初始值。但这里的“初始化”和下面的显示初始化阶段是有区别的，侧重点在于分配所需要的内存空间，不会去执行更进一步的 JVM 指令。
    - 解析：在这一步会将常量池中的符号引用（symbolic reference）替换为直接引用。在 Java 虚拟机规范中，详细介绍了类，接口，方法和字段等各方面的解析。
3. 初始化，这一步真正去执行类初始化的代码逻辑，包括静态字段赋值的动作，以及执行类定义中的静态初始化块内的逻辑，编译器在编译阶段就会把这部分逻辑整理好，父类型的初始化逻辑优先于当前类型的逻辑。
4. 类的使用
5. 类的卸载，执行垃圾回收，过期类被卸载


> [Java的ClassLoader机制](http://www.hollischuang.com/archives/199)

- Bootstrp loader  
  Bootstrp加载器是用C++语言写的，它是在Java虚拟机启动后初始化的，它主要负责加载%JAVA_HOME%/jre/lib,-Xbootclasspath参数指定的路径以及%JAVA_HOME%/jre/classes中的类。

- ExtClassLoader  
  Bootstrp loader加载ExtClassLoader,并且将ExtClassLoader的父加载器设置为Bootstrp loader.ExtClassLoader是用Java写的，具体来说就是 sun.misc.Launcher$ExtClassLoader，ExtClassLoader主要加载%JAVA_HOME%/jre/lib/ext，此路径下的所有classes目录以及java.ext.dirs系统变量指定的路径中类库。

- AppClassLoader  
  Bootstrp loader加载完ExtClassLoader后，就会加载AppClassLoader,并且将AppClassLoader的父加载器指定为 ExtClassLoader。AppClassLoader也是用Java写成的，它的实现类是 sun.misc.Launcher$AppClassLoader，另外我们知道ClassLoader中有个getSystemClassLoader方法,此方法返回的正是AppclassLoader.AppClassLoader主要负责加载classpath所指定的位置的类或者是jar文档，它也是Java程序默认的类加载器。

![img](./img/6752752-69487010ef6ab85a.png)

## 运行时数据区：深入理解堆/栈/元空间
Java虚拟机在运行时，会把内存空间分为若干个区域，根据《Java虚拟机规范（Java SE 7 版）》的规定，Java虚拟机所管理的内存区域分为如下部分：方法区、堆内存、虚拟机栈、本地方法栈、程序计数器。

![img](./img/20180807233055376.png)

### 方法区
方法区主要用于存储虚拟机加载的类信息、常量、静态变量，以及编译器编译后的代码等数据。在jdk1.7及其之前，方法区是堆的一个“逻辑部分”（一片连续的堆空间），但为了与堆做区分，方法区还有个名字叫“非堆”，也有人用“永久代”（HotSpot对方法区的实现方法）来表示方法区。

从jdk1.7已经开始准备“去永久代”的规划，jdk1.7的HotSpot中，已经把原本放在方法区中的静态变量、字符串常量池等移到堆内存中，（常量池除字符串常量池还有class常量池等），这里只是把字符串常量池移到堆内存中；在jdk1.8中，方法区已经不存在，原方法区中存储的类信息、编译后的代码数据等已经移动到了元空间（MetaSpace）中，元空间并没有处于堆内存上，而是直接占用的本地内存（NativeMemory）。根据网上的资料结合自己的理解对jdk1.3~1.6、jdk1.7、jdk1.8中方法区的变迁画了张图如下：

![img](./img/20180807233340374.png)

去永久代的原因有：
1. 字符串存在永久代中，容易出现性能问题和内存溢出。
2. 类及方法的信息等比较难确定其大小，因此对于永久代的大小指定比较困难，太小容易出现永久代溢出，太大则容易导致老年代溢出。
3. 永久代会为 GC 带来不必要的复杂度，并且回收效率偏低。

### 堆内存
堆内存主要用于存放对象和数组，它是JVM管理的内存中最大的一块区域，堆内存和方法区都被所有线程共享，在虚拟机启动时创建。在垃圾收集的层面上来看，由于现在收集器基本上都采用分代收集算法，因此堆还可以分为新生代（YoungGeneration）和老年代（OldGeneration），新生代还可以分为Eden、From Survivor、To Survivor。

### 程序计数器
程序计数器是一块非常小的内存空间，可以看做是当前线程执行字节码的行号指示器，每个线程都有一个独立的程序计数器，因此程序计数器是线程私有的一块空间，此外，程序计数器是Java虚拟机规定的唯一不会发生内存溢出的区域。

### 虚拟机栈
虚拟机栈也是每个线程私有的一块内存空间，它描述的是方法的内存模型，直接看下图所示：
![img](./img/20180807233512975.png)

虚拟机会为每个线程分配一个虚拟机栈，每个虚拟机栈中都有若干个栈帧，每个栈帧中存储了局部变量表、操作数栈、动态链接、返回地址等。一个栈帧就对应Java代码中的一个方法，当线程执行到一个方法时，就代表这个方法对应的栈帧已经进入虚拟机栈并且处于栈顶的位置，每一个Java方法从被调用到执行结束，就对应了一个栈帧从入栈到出栈的过程。

### 本地方法栈
本地方法栈与虚拟机栈的区别是，虚拟机栈执行的是Java方法，本地方法栈执行的是本地方法（Native Method）,其他基本上一致，在HotSpot中直接把本地方法栈和虚拟机栈合二为一，这里暂时不做过多叙述。

### 元空间
上面说到，jdk1.8中，已经不存在永久代（方法区），替代它的一块空间叫做“元空间”，和永久代类似，都是JVM规范对方法区的实现，但是元空间并不在虚拟机中，而是使用本地内存，元空间的大小仅受本地内存限制，但可以通过-XX:MetaspaceSize和-XX:MaxMetaspaceSize来指定元空间的大小。

## 垃圾回收机制
垃圾回收（Garbage Collection，GC），顾名思义就是释放垃圾占用的空间，防止内存泄露。有效的使用可以使用的内存，对内存堆中已经死亡的或者长时间没有使用的对象进行清除和回收。

### CMS(Concurrent Mark Sweep)
并发-标记-清除算法，针对老年代回收的GC

CMS以获取最小停顿时间为目的。

在一些对响应时间有很高要求的应用或网站中，用户程序不能有长时间的停顿，CMS可以用于此场景。 

1. 初始标记(STW)：该阶段进行可达性分析，标记GC ROOT能直接关联到的对象。会暂停用户线程。
2. 并发标记：和用户线程并发执行，由前阶段标记过的对象出发，所有可到达的对象都在本阶段中标记。
3. 并发预清理：标记从新生代晋升的对象、新分配到老年代的对象以及在并发阶段被修改了的对象。
    - 通过GC ROOT TRACING可到达确定老年代的对象是活着
    - 通过Minor GC扫描新生代
    - 通过CARD TABLE标记老年代
    - Minor GC通过扫描card table就可以很快的识别老年代引用新生代。
4. 重标记(STW)：暂停所有用户线程，重新扫描堆中的对象，进行可达性分析,标记活着的对象。
5. 并发清理：用户线程被重新激活，同时清理那些无效的对象。
6. 重置：CMS清除内部状态，为下次回收做准备。 



### G1
### ZGC
### Shenandoah
### Epsilon

## Java字节码执行引擎
## JKD性能监控于故障处理
## JVM服务器实战调优
### GCView
### Arthas

## JVM面试题解析

# MySQL
## MySQL优化基础
## 多版本并发控制MVCC
## 应用程序性能剖析
## 慢查询日志剖析
## 数据库优化与索引
## BTree索引/Hash索引/聚簇索引/非聚簇索引
## 索引的选择和表的维护
## 深入索引原理解析
## 高效索引策略与SQL查询优化
## SQL优化查询器

# Tomcat
## Tomcat概述
## Web引用通信机制
## Tomcat重要核心组件
## Tomcat类加载器
## Tomcat内部原理与线程模型
## HTTP协议和BIO/NIO
## Tomcat高可用集群架构方案
## Tomcat高可用集群架构实现
## Tomcat性能调用实战