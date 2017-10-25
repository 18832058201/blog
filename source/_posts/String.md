---
layout: post
title: String，StringBuffer，StringBuilder的区别
date: 2017-09-20 16:00
comments: true
categories: 随笔
tags: 
	- 面试知识点
	- String
---

# String，StringBuffer，StringBuilder的区别 #
这三个类的区别主要从以下两个方面去比较：
1. 是否可变：

- String类是使用字符数组来保存字符串的，从他的源码中可以看出，有“final关键字”修饰，因此String类是不可变的。当“改变String对象”时，并不是真的改变String对象，而是<label style='color:red'>创建一个新的String对象</label>，然后将引用指向新的String对象。所以经常改变内容的字符串最好不要用 String ，因为每次生成对象都会对系统性能产生影响，特别当内存中无引用对象多了以后，JVM 的 GC 就会开始工作，那速度是一定会相当慢的。这时用StringBuffer或StringBuilder比较好。
<!-- more -->
```java
private final char value[];
```
- StringBuffer和StringBuilder都是继承AbstractStringBuilder类，AbstractStringBuilder类中也是使用字符数组来保存字符串，从源码可以看出他们都是可变的。
```java
char value[];
```
2. 是否多线程安全

- String对象时不可变的，存在常量池中，创建后就不可改变，所以是线程安全的。
- StringBuffer对方法或者调用的方法加了同步锁，所以是线程安全的。
- StringBuilder并没有对方法加同步锁，所以是非线程安全的。

3. String，StringBuffer和StringBuilder的使用场景：
根据上边分析的区别可以总结以下几点：
- 当字符串经常被改变时，建议采用StringBuilder或StringBuffer
- 在单线程程序中使用StringBuilder的效率高于StringBuffer
- 在多线程中使用String或StringBuffer以保证线程安全

4. 效率分析
在字符串拼接时，例如以下的两种拼接：
String s1 = "hello" + "world" ;
StringBuffer s2 = new StringBuffer("hello").append("world");
这种情况下s1的拼接效率比s2拼接快的多，因为在虚拟机眼里，s1拼接的两个字符串都是字符串常量，存在于常量池中，在编译时会直接把他当成一个常量看待，所以在执行s1拼接时基本不耗时。
一般情况下：三者效率高低如下
<label style="color:red">StringBuilder>StringBuffer>String</label>
jdk1.5之后，字符串的+加法运算,已经隐式采用StringBuilder来实现的

