---
layout: post
title: 面试题
category: 面试题
keywords: 阅读,书单,2013

---

#### 1.面试题

1.请描述synchronized和reentrantlock的底层实现及重入的底层原理-百度阿里

monitorenter、monitorexit、AQS；重入原理：entry count，state。

2.请描述锁的四种状态和升级过程-百度阿里

无锁、偏向锁、轻量级锁、重量级锁

3.CAS的ABA问题如何解决-百度

atomicStampedReference，还可用带boolean版本戳的atomicMarkableReference

4.请谈一下AQS，为什么AQS的底层是CAS+volatile-百度

state是volatile修饰的，并且设置state的方法除了有setState还有compareAndSetState

5.请谈一下你对volatile的理解-美团阿里
6.volatile的可见性和禁止指令重排序是如何实现的-美团

可见性：缓存一致性协议 指令完成数据的读写，通过其中load和store指令互相组合成的4个内存屏障实现禁止指令重排序

7.CAS是什么-美团
8.请描述一下对象的创建过程一美团顺
9.对象在内存中的内存布局-美团顺丰
10.DCL单例为什么要加volatile-美团    
11.解释一下锁的四种状态-顺丰
12.Object o=new Object()在内存中占了多少字节-顺丰

16=12(markword8+classpointer4)+padding4

13.请描述synchronized和ReentrantLock的异同一顺丰
14.聊聊你对as-if-serial 和happens-before 语义的理解-京
15.你了解ThreadLocal吗？你知道ThreadLocal中如何解决内存泄漏问题吗？-京东阿里
16.请描述一下锁的分类以及JDK中的应用-阿里
17.问：自旋锁一定比重量级锁效率高吗？-阿里
18.打开偏问锁是否效率一定会提升？为什么？

#### 2.JVM和GC

1.cms和G1的异同百度  

2.G1什么时候引发 Full GC-百度  

3.说一个最熟悉的垃圾回收算法-百度  

4.吞吐量优先和响应时间优先的回收器有哪些-百度  

parallel scavenge 和 CMS

5.怎么判断内存泄漏-顺丰  

6.讲一下CMS的流程-顺丰  

7.为什么压缩指针超过32G失效-京东  

8.什么是内存泄漏?GC调优有经验吗?一般出现GC问题你怎么解决?-淘宝  

9.ThreadLocal有没有内存泄漏问题-阿里蘑菇街  

10.G1两 Region个不是连续的,而且之间还有可达的引用，我现在要回收一个,另一个怎么处理?-阿里 

11.讲一下JVM堆内存管理(对象分配过程)-阿里  

12.听说过CMS的并发预处理和并发可中断预处理吗-阿里   

13.到底多大的对象会被直接扔到老年代-阿里

#### 3.其他

1.除了CAS、原子类、sync、Lock还有什么线程安全方式？

final

2.hashmap和hashtable的异同？为什么hashtable被弃用？

；1.7和1.8之后的区别？

3.允许null键的map你知道哪些？null放在hashmap的哪里？

hashmap、linkedhashmap、weakhashmap；底层数组的0号位置；

4.为什么hashtable的扩容是2倍+1？hashmap的容量为什么设置为2的次幂？

从除留余数法，hashtable初始容量回答；

5.红黑树的插入时间复杂度

6.解决哈希冲突的方式？

7.现有1T数据，内存1G，怎么排序？

外部排序，多路归并；

8.Tomcat为什么要重写类加载器？

9.tcp握手挥手过程及其状态转换

10.你知道哪些什么设计模式？在jdk中怎样体现？

工厂、责任链、观察者、建造、代理、单例。

11.类加载全过程

12.线程池7个参数？线程池拒绝策略分别使用在什么场景？

13.java内存模型

jmm共享内存模型以及8个原子操作指令

14.什么叫做阻塞队列的有界和无界？

15.cookie和session介绍一下

16.说一下反射，反射会影响性能吗？

17.JUC包里的同步组件主要实现了AQS的哪些主要方法？

tryAcquire、tryRelease、tryAcquireShared、tryReleaseShared、isHeldExclusively

#### 4.美团

1.concurrentHashMap底层原理

2.手写一个LRU，用linkedHashMap

3.hashmap底层数据结构，为什么用红黑树不用普通的AVL树？为什么在8的时候链表变成树？为什么在6的时候从树退回链表？

数组+链表+红黑树；平衡；根据泊松分布概率（源码注释里写了）；

3.线程池7个参数，该怎么配置最好？

4.priorityQueue底层是什么，初始容量是多少，扩容方式呢？

最小堆，11，若原始大小<64，则扩容为原来的2倍+2，不然就扩容为原来的1.5倍

5.跳表，什么场景会用到？

concurrentSkipListMap，哦用在多线程下需自定义排序顺序时

6.copyOnWriteArrayList知道吗，迭代器支持fail-fast吗？

线程安全ArrayList，写时复制，迭代器是采用快照风格，不支持fail-fast

7.innodb的底层数据结构？为什么用B+树不用B树？为什么不用红黑树？

8.编码：无需数组怎么寻找第K大的树？写一个二叉树层次遍历。

9.不知道大小的数据流取其中100个数，怎样的取法最随机

10.n个物品每个物品都有一定价值，分给2个人，怎么分两个人的价值差最小

11.假设百度每个页面能放100个网页，每个页面都有一个评分，怎样快速找到第8页的所有网页

12.千万级数据量的list找一个数据（多线程），抢红包

13.百万级int数据量的一个array求和（fork/join）



#### 5.顺丰

1.线程池的设计里体现了什么设计模式？

享元模式

2.说说你了解什么设计模式，知道责任链设计模式吗？

3.wait/notify体现了什么设计模式？

观察者模式

4.谈一下spring事务传播？IOC底层原理？AOP的底层实现？

；xml+dom4j+工厂+单例；后置处理器、动态代理（接口、cglib）、ASM；

5.怎么判断内存泄漏？怎么在日志里排查错误，该用哪些linux命令？

6.mysql原子性和持久性怎么保证？

undolog，redolog

10.怎么解决幻读？

MVCC+间隙锁

11.innodb和myisam区别？索引分类？

12.对象的创建过程？对象内存的存储布局？对象头包括什么？对象怎么定位？

；对象头、类元指针、示例数据、对齐填充；锁、hashcode、GC；直接指针、句柄；

13.堆的划分？对象怎么分配？

；栈上分配->TLAB->老年代->新生代

#### 6.京东

1.总体说下集合框架

2.怎么看待接口和抽象类

3.索引的分类？主键索引的设计应该采用 B-tree 索引还是 hash 索引？

4.设计模式说 5，6 个

5.谈一谈 DDD 面向领域编程

6.说一下 hibernate 一级缓存和二级缓存

7.说一下你了解的 MQ？disruptor单机性能最高的MQ，600WQPS/sec？

8.谈一谈你对高并发的理解，你会从什么角度设计高并发程序

9.JUC 包里的限流该怎么做到

Semaphore/ guava ratelimiter

10.索引不适用的条件？索引的分类？

索引列上有函数、不满足最左前缀、使用了不等号、使用了范围查询等；B-tree、hash、全文、单值、唯一、复合、聚簇、非聚簇等；

11.说一下 NIO 和 AIO

12.AIO 里用到什么设计模式

13.说一下 select，poll，epoll

14.谈一下 TCP 的拥塞控制

15.你知道什么是 as-if-serial 语义吗，它和 happen-before 语义有什么区别

16.Executors 创建线程池的方式

17.CachedThreadPool 里面用的什么阻塞队列

18.那你知道 LinkedTransferQueue 吗，和 SynchronousQueue 有什么区别

19.你还知道什么阻塞队列，能具体说说它们的特点吗

20.线程池的线程数怎么设置比较好

首先确定任务是计算密集型还是IO密集型，

21.你知道新出的 LongAdder 吗，和 AtomicLong 有什么区别？知道 LongAccumulator 吗？

22.锁的分类及特点

乐观锁、悲观锁；自旋锁、读写锁、分段锁、排它锁、共享锁；

23.redis中sorted sets底层数据结构？

1. 进程间通信有哪些，请详细说明一下自己在哪种场景下用过哪种方式？
2. 死锁是怎么产生的？说一下
3. Java 中的线程有几种状态？
4. os 中管道的实现
5. 解释一下分段和分页
6. 虚拟地址、逻辑地址、线性地址、物理地址的区别
7. 协程和线程和进程的区别，你是怎样理解的？
8. 为什么三次握手四次挥手？三次挥手可不可以？
9. OSI 和 TCP/IP 的区别
10. http server 服务，现在要做一个针对用户维度或者接口维度的频控，假设一秒 100 这种，问在不改变原有接口服务的情况下，你如何实现？
11. 网络安全相关，csrf 这种 *** 如何防范
12. 各种协议问我了解过没有，例如 TCP/UDP/ICMP, 这个问题比较常见
13. 微服务和 http 服务的区别，你对两者是怎样理解的？
14. 你自己使用 MySQL 中遇到过乱码问题没有，如何解决的，产生原因是什么？
15. Select * From table_name where filed_name != NULL 这个 sql 语句是什么意思，你觉得有没有问题？
16. 关键字 where 和 having 的区别，说一下
17. 介绍一下 MySQL 数据库引擎 innodb，及 MySQL 的四种隔离级别
18. 用过什么索引，使用这个索引有什么要注意的
19. 数据的分库分表会产生什么问题，如何解决？
20. 写一个 sql 语句，给表 t_score 字段 id (int),score (varchar),team1_id (int),team2_id (int)
21. 给表 t_team 字段 id (int),name (varchar)，完成输出这种效果的语句（一条完成）：id:xx,team1_name: 中国，team2_name: 日本，score:4:1

