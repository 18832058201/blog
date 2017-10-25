---
layout: post
title: HashMap和HashTable的区别
date: 2017-09-21 20:30
comments: true
categories: 集合
tags: 
	- HashMap
	- HashTable
---
# 继承的父类不同 #
HashTable继承自Dictionary类，而HashMap继承自AbstractMap类。但二者都实现了Map接口。

# 线程安全性不同 #
HashTable中的方法是Synchronized的，而HashMap中的方法在缺省情况下是非Synchronize的。在多线程并发的环境下，可以直接使用Hashtable，不需要自己为它的方法实现同步，但使用HashMap时就必须要自己增加同步处理。所以HashMap的执行效率比HashTable要高。实际上HashTable集合已经被抛弃掉了，平时也不建议使用HashTable，而是使用HashMap，在多线程需要满足线程安全时自己去实现同步。

<!-- more -->
# key和value是否允许null值 #
<label style='color:blue'>HashMap中允许</label>使用key或value为空（null）
<label style='color:blue'>HashTable中不允许</label>key或value为空（null）
两者的key都不能有重复，而value可以重复

# 两个遍历方式的内部实现上不同 #
Hashtable、HashMap都使用了 Iterator。而由于历史原因，Hashtable还使用了Enumeration的方式 。

# hash值不同 #
哈希值的使用不同，HashTable直接使用对象的hashCode。而HashMap重新计算hash值。
Hashtable计算hash值，直接用key的hashCode()，而HashMap重新计算了key的hash值，Hashtable在求hash值对应的位置索引时，用取模运算，而HashMap在求位置索引时，则用与运算，且这里一般先用hash&0x7FFFFFFF后，再对length取模，&0x7FFFFFFF的目的是为了将负的hash值转化为正值，因为hash值有可能为负数，而&0x7FFFFFFF后，只有符号外改变，而后面的位都不变。

# 内部实现使用的数组初始化和扩容方式不同 #
HashTable在不指定容量的情况下的默认容量为11，而HashMap为16，Hashtable不要求底层数组的容量一定要为2的整数次幂，而HashMap则要求一定为2的整数次幂。
Hashtable扩容时，将容量变为原来的2倍加1，而HashMap扩容时，将容量变为原来的2倍。

