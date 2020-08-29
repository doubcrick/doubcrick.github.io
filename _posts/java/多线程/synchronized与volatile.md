#### 1.java的线程模型（对比go语言的线程模型）

线程模型主要有三种：1、内核级别线程；2、用户级别线程；3、混合线程

java线程模型采用内核级别。与操作系统内核线程为1比1的关系，由内核调度器调度（重量级线程），线程切换之间要经历x80中断，线程个数被内核限制。

go语言线程又叫纤程(协程goroutine)，轻量级线程。使用了所谓的 N:M 调度的技术实现了自己的调度器，它在 N 个系统线程上多路复用（或调度）M 个协程，也就是说由 n 个系统线程，生成了 m 个 go 的协程。

区别：

栈的大小：

1.java线程固定分配内存块（1M）；

2.协程（2K）可自动调整其大小； goroutine 没有ThreadLocal，协程从逻辑上来说也是线程。

调度方式：

1.java由操作系统调度及切换，将一个用户线程的状态保存到内存，恢复另一个用户线程的状态，并且更新调度程序的数据结构，导致上下文切换比较慢。

2.自己实现的调度器，N个系统线程调度M个协程。

#### 2用户态与内核态

为什么要分？

一个应用程序可以直接干掉操作系统。

用户态的程序要访问一些比较危险的操作的时候，比如格式化硬盘或直接访问内存网卡等，必须经过操作系统即内核的允许，这样可以保证安全性。

从指令来讲，用户态只能执行某些指令，而内核态可以执行所有指令。

`int 0x80` 向 OS 申请一个中断的调用

#### 3.CAS

  Compare And Swap  (Compare And Exchange)/自旋/自旋锁/无锁(无重量锁)

因为经常配合循环操作,直到完成为止,所以泛指一类操作  cas(v,a,b)，变量v.期待值a，修改值b

ABA问题,你的女朋友在离开你的这段儿时间经历了别的人,自旋就是你空转等待,一直等到她接纳你为止  

解决办法(版本号AtomicStampedReference),基础类型简单值不需要版本号

#### 4.Unsafe

Unsafe通过启动类加载器加载，因此必须用反射来拿

主要用于CAS

```java
private boolean compareAndSwapState(int expect, int update) {
    return unsafe.compareAndSwapInt(this,stateOffset,expect,update);
}
```

通过 JNI 访问本地的 C++ 库。

```c++
inline jint     Atomic::cmpxchg    (jint     exchange_value, volatile jint*     dest, jint     compare_value) {  
    int mp = os::is_MP();  __asm__ volatile (LOCK_IF_MP(%4) "cmpxchgl %1,(%3)"                    : "=a" (exchange_value)                    
                   : "r" (exchange_value), "a" (compare_value), "r" (dest), "r" (mp)               : "cc", "memory");  
    return exchange_value; }
```

inline 内联方法，解决一些频繁调用的函数大量消耗栈空间（栈内存）的问题
is_MP 代表是否多核处理器，源码请跳转第五步实现为
__asm__ volatile (C 语言内嵌汇编)
LOCK_IF_MP（汇编指令：如果多个 cpu 则进行锁定，源码请跳转第六
cmpxchgl （汇编指令：compile and exchange 硬件直接支持）

最终执行汇编指令：cmpxchg=cas修改变量值

`lock cmpxchg 指令`

cmpxchg 不具有原子性，lock 指令在执行后面指令的时候锁定一个北桥电信号,比锁定总线轻量，当执行 cmpxchg 其他 cpu 不允许做修改，所以 lock cmpxchg 具有原子性。

#### 5.MarkWord

对象布局：

对象头：MarkWord+class pointer（默认压缩4byte）+length（数组长度）

MarkWord：hashcode、锁、GC信息

对象：MarkWord+class pointer（默认压缩4byte） + instance data + padding



#### 6.工具JOL（javaObjectLayout）

**对象的内存布局**：当 new 出一个对象之后，这个对象在内存是怎么分布的？
我们来看 HotSpot 的实现。

**要求对象 8 字节对齐（对象大小字节数必须是 8 的整数倍，如果不是，补 4 个空字节）**

> 8 字节 markword
> 4 字节 classpointer（默认）
> 4 字节 instance data（用于存储成员变量）

大端和小端：

举例来说，数值 0x2211 使用两个字节储存：高位字节是 0x22，低位字节是 0x11。

- 大端字节序（Big-Endian:）：高位字节在前，低位字节在后，这是人类读写数值的方法。
- 小端字节序（Little-Endian）：低位字节在前，高位字节在后，即以 0x1122 形式储存。

小端好处：计算机电路先处理低位字节，效率比较高，因为计算都是从低位开始的。所以，计算机的内部处理都是小端字节序。
大端好处：人类还是习惯读写大端字节序。所以，除了计算机的内部处理，其他的场合（JAVA）几乎都是大端字节序，比如网络传输和文件储存。

#### 7.synchronized的横切面详解

##### 	7.1java源码层级

synchronized（object）

##### 	7.2字节码层级

monitorenter monitorexit

##### 	7.3JVM层级（Hotspot）

InterpreterRuntime:: monitorenter 方法

inflate 方法：膨胀为重量级锁

#### 8.锁升级过程

无锁 - 偏向锁 - 轻量级锁- 重量级锁（OS帮管理[队列]）

synchronized 优化的过程和 markword 息息相关，用 markword 中最低的三位代表锁状态 其中 1 位是偏向锁位 两位是普通锁位

偏向锁：没有必要设计锁竞争机制时，只是把第一个访问的线程的id 写到 markword 中，而不去真正的加锁
轻量级锁：偏向锁时，有人来竞争锁了，现在操作系统把偏向锁撤销，进行自旋锁（轻量级锁）竞争。竞争方式：每个人在自己的线程内部生成一个自己 LR（Lock Record 锁记录），两个线程通过自己的方式尝试将 LR 写门上，竞争成功的开始运行，竞争失败的一直自旋等待。
重量级锁：当必须加锁时，markword 中记录的是 objectmonitor（JVM 用 C++ 写的一个 Object）【monitorenter、monitorexit】

##### 8.1 JDK8 MarkWord实现表

自旋锁什么时候升级为重量级锁？一定次数

为什么有自旋锁还需要重量级锁？性能

偏向锁是否一定比自旋锁效率高？不一定。在明确知道一个资源会有多线程竞争的情况下，偏向锁肯定会涉及锁撤销。这时候应给直接使用自旋锁

偏向锁有延时？默认4秒，因为JVM有一些默认启动的线程如GC，其中sync代码启动就肯定有竞争，如使用偏向锁造成不断进行锁撤销和锁升级的操作。

偏向锁过程？把MarkWord的线程id改为自己的线程id的过程

轻量级锁过程？撤销偏向锁，升级轻量级锁，线程在自己的线程栈生成LockRecord，用CAS操作将MarkWord设置为指向自己这个线程的LR。

重量级锁过程？向操作系统申请资源，Linux mutex，CPU从3级-0级系统调用，线程挂起，进入等待队列，等待操作系统的调度，然后再映射回用户空间。自适应自旋。

当Java处在偏向锁、重量级锁状态时，hashcode值存储在哪？当一个对象已经计算过identity hash code，它就无法进入偏向锁状态；轻量级锁存在LR中，重量级锁存在Objectmonitor的成员中。

##### 8.2 锁重入
synchronized 是可重入锁（在未解锁的情况下，可以多次被加锁）。
用来满足子类和父类都是 sync 的情况。
重入次数必须记录，因为要解锁几次必须要对应。
偏向锁实现：记录在线程栈里，自旋锁 -> 线程栈 -> 每重入一次，LockRecord+=1
重量锁实现：惊动了操作系统，记录在 ObjectMonitor 字段上

##### 8.3synchronized最底层实现

lock comxchg ...指令

##### 8.4synchronized vs Lock（CAS）

在争用高耗时的环境下synchronized效率更高

在低争用低耗时的环境下CAS效率更高

synchronized到重量级之后是等待队列（不消耗CPU）

CAS（等待期间消耗CPU）

#### 9.锁消除 lock eliminate

依赖逃逸分析开启，一个对象不会逃逸出线程，无法被其它线程访问到，那该对象的读写就不会存在竞争。

#### 10.锁粗化 lock coarsening

粗化是两段加锁代码之间执行很快的代码可以放在一个锁中，锁消除是JVM在JIT即时编译时对上下文扫描去除不可能存在共享资源竞争的锁，如方法内部使用stringbuffer。

#### 11.锁降级（不重要）

只要被VMThread访问，降级就没意义，可认为降级不存在。

#### 12.超线程

一个ALU（计算单元）+两组Registers（寄存器）+PC

超线程技术能够把一个物理处理器在软件层变成两个逻辑处理器，可以使处理器在某一时刻，同步并行处理更多指令和数据 (多个线程)，当然了实际效能不可实现双倍提升，毕竟干活的核心只有一个。

#### 13.参考资料

#### 14.volatile的用途

##### 14.1线程可见性

lock 前缀指令（cacheline） + 缓存一致性协议[MESI -inter CPU的协议]

其他一致性协议：MSI\MESI\MOSI\Synapse\Firefly\Dragon

MESI协议：modified、exclusive独占、shared共享、invalid失效

过程：

- Thread-A 发出 LOCK# 指令
- 发出的 LOCK# 指令锁总线（或锁缓存行），同时让 Thread-B 高速缓存中的缓存行内容失效（嗅探）
- Thread-A 向主存回写最新修改的 i

- Thread-B 读取变量 i，发现对应地址的缓存行被锁了，等待锁的释放，缓存一致性协议会保证它读取到最新的值

##### 14.2防止指令重排序

###### 	14.2.1 DCL单例需不需要加volatile

​		对象初始化过程分三步：申请内存空间，此时其内部对象处于半初始化（如int都为0），调用构造方法复制，指向该对象。如后两步乱序可能造成使用到了半初始化的对象。

###### 	14.2.2 CPU的基础知识

CPU的制作：

一堆沙子+一堆铜+一堆胶水+特定金属添加+特殊工艺
沙子脱氧>石英>二氧化硅>提纯->硅锭->切割->晶圆->涂抹光刻胶->光刻->蚀刻->清除光刻胶->电镀->抛光->铜层->测试-切片->封装

CPU的计算：

硅->加入特殊元素>P半导体 N半导体>PN结->二极管->场效应晶体管->逻辑开关
与门 或门 非门 或非门 (异或)->基础逻辑电路
加法器 累加器 锁存器  
实现手动计算(通电一次,运行一次位运算) 
加入内存 实现自动运算(每次读取内存指令,(高电低电))

CPU的数据读取：

按块读取，可以提高效率（程序局部性原理），充分发挥总线CPU针脚等一次性读取更多数据的能力。

CPU的读取速度ALU：

​	registers < 1ns

​	L1 cache 约1ns

​	L2 cache 约3ns

​	L3 cache 约15ns

main memory 约80ns

缓存行cacheline：

缓存行越大，局部性空间效率越高，但读取时间慢，越小则相反，目前多用的64字节。

###### 	14.2.3 系统底层如何实现数据一致性

MESI缓存一致性协议

###### 	14.2.4系统底层如何保证有序性

lock addl

lock用于在多处理器中执行指令时对共享内存的独占使用。它的作用是能够将当前处理器对应的缓存的内容刷新到内存，并使其他处理器对应的缓存失效。还提供了有序的指令无法越过这个内存屏障的作用。

###### 	14.2.5volatile如何解决指令重排序

内存屏障，happens-before、as-if-serial(单线程)

每个 volatile 写操作前面，加 StoreStore 屏障，禁止上面的普通写和他重排；每个 volatile 写操作后面，加 StoreLoad 屏障，禁止跟下面的 volatile 读 / 写重排

每个 volatile 读操作后面，加 LoadLoad 屏障，禁止下面的普通读和 voaltile 读重排；每个 volatile 读操作后面，加 LoadStore 屏障，禁止下面的普通写和 volatile 读重排

X86CPU内存屏障：

sfence(store):在 sfence指令前的写操作当必须在sfence指令后的写操作前完成 
Ifence(load):在 lfence指令前的读操作当必须在lfence指令后的读操作前完成 
mfence:在 mfence指令前的读写操作当必须在mfence指令后的读写操作前完成

#### 15.用hsdis观察synchronized和volatile

##### 	15.1输出结果

#### 16.负数的二进制表示

整数 - 1 在计算机中如何表示。

假设这也是一个 int 类型，那么： 

1、先取 - 1 的原码：10000000 00000000 00000000 00000001 

2、得反码： 11111111 11111111 11111111 11111110（除符号位按位取反） 

3、得补码： 11111111 11111111 11111111 11111111 

可见，－1 在计算机里用二进制表达就是全 1。16 进制为：0xFFFFFF

**原码：一个正数，按照绝对值大小转换成的二进制数；一个负数按照绝对值大小转换成的二进制数，然后最高位补 1，称为原码。** 

**反码：正数的反码与原码相同，负数的反码为对该数的原码除符号位外各位取反。**

**补码：正数的补码与原码相同，负数的补码为对该数的原码除符号位外各位取反，然后在最后一位加 1**

#### 17.面试题

请描述synchronized和reentrantlock的底层实现及重入的底层原理-百度阿里
请描述锁的四种状态和升级过程-百度阿里
CAS的ABA问题如何解决-百度
请谈一下AQS，为什么AQS的底层是CAS+volatile-百度
请谈一下你对volatile的理解-美团阿里
yolaltle的可见性和禁止指令重排序是如何实现的-美团aoa
CAS是什么-美团
请描述一下对象的创建过程一美团顺
对象在内存中的内存布局-美团顺丰
DCL单例为什么要加volatile-美团    
解释一下锁的四种状态-顺丰
Object o=new Object()在内存中占了多少字节-顺手
请描述synchronized和ReentrantLock的异同一顺丰伞
聊聊你对as-if-serial 和happens-before 语义的理解-京
你了解ThreadLocal吗？你知道ThreadLocal中如何解决内存泄漏问题吗？-京东阿里
请描述一下锁的分类以及JDK中的应用-阿里
问：自旋锁一定比重量级锁效率高吗？-阿里
打开偏问锁是否效影一定会提升？为什么？