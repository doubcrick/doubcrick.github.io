---
layout: post
title: 集合
category: base
keywords: collection
---

### 1、什么是集合？

集合是用来存储对象的数据结构。

### 2、java的集合有哪些？

![](https://i.loli.net/2020/11/04/Skv6jcEyhTWgdXt.png)

queue：对象数组

SynchronousQueue：没有容量，放入一个必须等待去除后才能再次放入。

LinkedTransferQueue ：链表实现，阻塞机制类似同步队列，放入数据时检测到有等待的线程先唤醒线程。

### 3、其他

1.hashmap

hashmap 底层扩容线程安全问题？哈希环

如果一个对象要作为 hashmap 的 key 需要做什么？实现hashcode和equals方法

hash 算法：为什么要⾼位和低位做异或运算？让⾼位也参与 hash 寻址运算，降低 hash 冲突

hash 寻址：为什么是 hash 值和数组.length - 1 进⾏与运算？因为取余算法效率很低，按位与运算效率⾼

扩容机制：数组 2 倍扩容，重新寻址（rehash），hash & n - 1，判断⼆进制结果中是否多出⼀个 bit 的 1，如果没多，那么就是原来的 index，如果多了出来，那么就是 index + oldCap，红⿊树，查找的性能， 是 O (logn)

2.线程同步方式，具体每一个怎么做的

