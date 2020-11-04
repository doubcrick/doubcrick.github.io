---
layout: post
title: 集合
category: base
keywords: collection
---

### 1、什么是集合？

集合是用来存储对象的数据结构。

### 2、java的集合有哪些？

![](https://i.loli.net/2020/11/03/b3tCO6ihvjsKlga.png)

### 2、java是如何执行的？

java文件->class文件->类加载器->字节码校验->执行入口->解释器/JIT->硬件

![](https://i.loli.net/2020/11/02/cP4gBhlRAspj6X2.png)

### 3、java提供的小工具

jps：输出 JVM 中运行的进程状态信息，常用jps -l，类似于ps -ef|grep java

jmap：查看堆内存使用状况,常用jmap -dump:live,format=b,file=ifix.hprof 22060，导出文件后使用MAT、VisualVM、jhat等工具分析内存泄露。dump操作为将整个heap写入文件，过程中将会暂停应用，对并发量较高的线上应用谨慎使用，推荐使用arthas。

jhat：jhat.exe -port 9999 ifix.hprof

jstack：查看某个 Java 进程内的线程堆栈信息，常用jstack -l [pid]，通过该工具可以查看死锁、线程状态、找出该进程内最耗费 CPU 的线程等。

jstat：JVM 统计监测工具，常用jstat -gc 22060 2s 4，查看gc情况。

javap：将class文件进行指令级的反编译，常用 javap -c Test.class。

jinfo：查看运行的JVM参数信息，常用jinfo -flags 22060。

### 4、其他

hashmap 底层扩容线程安全问题

如果一个对象 要作为 hashmap 的 key 需要做什么？

线程同步方式，具体每一个怎么做的

