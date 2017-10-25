---
layout: post
title: JVM（一）--- Java内存区域与内存溢出异常
date: 2017-09-21 11:00
comments: true
categories: JVM
tags: 
	- Java内存区域
	- 内存溢出异常
---
# Java运行时数据区域 #
Java虚拟机运行时数据区如图所示：
<img style='width:500px;height:400px;' src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/2017092111801.jpg">
<!-- more -->
## 程序计数器(线程私有内存) ##
> 程序计数器（Program counterRegister）是一块较小的内存空间，它可以看做是当前线程所执行的字节码的行号指示器。

**线程私有内存：**由于Java虚拟机的多线程是通过轮流切换并分配处理器执行时间的方式来实现的，在任何一个确定的时刻，一个处理器（对于多核处理器来说是一个内核）都只会执行一条线程中的指令。因此为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各条线程之间计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

如果线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；如果正在执行的是Native方法，这个计数器值为空（Undefined）。<label style='color:red'>此内存区域是唯一一个在Java虚拟机规范中没有规定任何OutOfMemoryError情况的区域。</label>

## Java虚拟机栈（线程私有内存） ##
> Java虚拟机栈（Java Virtual Machine Stacks）也是线程私有的，它的生命周期与线程相同。<label style="color:blue">虚拟机描述的是Java方法执行的内存模型</label>：每个方法在执行的同时会创建一个栈帧（Stack Frame），用于存储<label style="color:blue">局部变量表、操作数栈、动态链接、方法出口</label>等信息。每个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

局部变量表存放了编译期可知的各种<label style='color:blue'>基本数据类型</label>（boolean、byte、char、short、int、float、long、double）、<label style='color:blue'>对象引用类型</label>（reference类型，它不等同与对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或其他与此对象相关的位置）和<label style='color:blue'>returnAddress类型</label>（指向了一条字节码指令的地址）。

其中64位长度的long和double类型的数据会占用2个局部变量空间（Slot），其余的数据类型只占用1个。局部变量表所需的内存空间在编译期间完成分配，当进入一个方法时，这个方法需要在帧中分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表的大小。

在Java虚拟机规范中，对这个区域规定了两种异常状况：
1.<label style='color:blue'>如果线程请求的栈深度大于虚拟机所允许的深度</label>，将抛出`StackOverflowError`异常；
2.如果虚拟机栈可以动态扩展，<label style='color:blue'>如果扩展时无法申请到足够的内存</label>，就会抛出`OutOfMemoryError`异常

## 本地方法栈（线程私有内存） ##
本地方法栈(Native Method Stack)与虚拟机栈所发挥的作用是非常相似的，它们之间的区别不过是虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的Native方法服务。在虚拟机规范中对本地方法栈中方法使用的语言、使用方式与数据结构并没有强制规定，因此具体的虚拟机可以自由实现它。甚至有的虚拟机（譬如Sun HotSpot虚拟机）直接就把本地方法栈和虚拟机栈合二为一。与虚拟机栈一样，本地方法栈区域也会抛出`StackOverflowError`和`OutOfMemoryError`异常。

## Java堆 ##
对于大多数应用来说，<label style='color:blue'>Java堆（Java Heap）是Java虚拟机所管理的内存中最大的一块</label>。<label style='color:blue'>Java堆是被所有线程共享的一块内存区域，</label>在虚拟机启动时创建。<label style='color:blue'>此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。</label>

Java堆是垃圾收集器管理的主要区域，因此很多时候也被称作“GC堆”（Garbage Collected Heap）。

根据Java虚拟机规范的规定，Java堆可以处于物理上不连续的内存空间中，只要逻辑上是连续的即可，就像我们的磁盘空间一样。在实现时，既可以实现成固定大小的，也可以是可扩展的，不过当前主流的虚拟机都是按照可扩展来实现的（通过-Xmx和-Xms控制）。<label style='color:blue'>如果在堆中没有可用内存去完成实例分配，并且对也无法再扩展时，将会抛出`OutOfMemoryError`异常.</label>

## 方法区 ##
方法区域Java堆一样，是各个线程共享的内存区域，<label style='color:blue'>它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。</label>虽然Java虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做Non-Heap（非堆），目的应该是与Java堆区分开来。

根据Java虚拟机规范的规定，<label style='color:blue'>当方法区无法满足内存分配需求时，将抛出`OutOfMemoryError`异常。</label>