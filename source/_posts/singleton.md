---
layout: post
title: 单例模式(Singleton)
date: 2017-09-10 20:00
comments: true
categories: 设计模式
tags: 
	- 单例模式
---
# 什么是单例模式？ #
单例模式有以下特点：
- 单例类只能有一个实例
- 单例类必须自己创建自己的唯一实例
- 单例类必须给所有其他对象提供这一实例
**单例模式确保某个类只有一个实例，而且自行实例化并向整个系统提供这个实例**

<!--more-->

# 如何实现单例模式？ #
单例模式的实现常见的有“懒汉式”和“饿汉式”
## 1. “饿汉式”单例模式的Java实现：（线程安全） ##
```java
/**
 * 作者：陈德华
 * 时间：2017/9/17 9:06
 * 描述：饿汉式的单例模式实现
 * 饿汉式单例模式天生就是多线程安全的，因为在初始化类的时候就已经创建类
 * 当线程访问时只需直接拿到实例就可以了，无需创建，就不会可能出现多个线
 * 程同时实例化出多个实例的情况
 */
public class Singleton {
    //私有化构造器，不让外部类实例化此类
    private Singleton (){}
    //直接创建一个实例，在类初始化的时候就实例化
    private static final Singleton instance = new Singleton();
    //定义一个公开的static方法供外部访问得到实例
    public static Singleton getInstance(){
        return instance;
    }
}
```
> 小结：“饿汉式”的单例模式是创建一个final实例，该实例在类初始化的时候被创建，外部访问的时候直接返回此实例就可以，那么就不存在多线程访问时可能创建多个实例的情况，所以<font style="color:red">“饿汉式”的单例模式是天生线程安全的。</font>

## 2.“懒汉式”单例模式的Java实现：（单线程中） ##
```java
/**
 * 描述：懒汉式的单例模式实现一
 * 以下这种实现创建类的过程是在调用getInstance方法时发生的
 * 这种方式就可能出现第一次多个线程在同时访问这个方法时创建出多个实例
 * 所以是非线程安全的，这种实现只适合于单线程中
 */
public class Singleton {
    //私有化构造器，不让外部类实例化此类
    private Singleton (){}
    //定义一个对象引用
    private static  Singleton instance;
    public static Singleton getInstance(){
        //第一次访问时instance为空，此时会实例化instance，之后的每次访问就直接返回该实例
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
```
> 小结：以上这种方式虽然实现了单例模式，但只适用于单线程中，因为在多线程中，当instance还没有实例化的时候，有多个线程同时访问getInstance方法时，就会实例化出多个对象，这样就导致线程不安全。要让其满足线程安全见下边的实现方式3

## 3. “懒汉式”单例模式的Java实现：（线程安全） ##
```java
/**
 * 描述：懒汉式的单例模式实现二
 * 以下这种实现解决了多线程安全问题，在instance实例的步骤上加上同步锁
 * 因为在同一时刻，只能有一个线程能拥有同步锁，其他线程只能等待执行，所以
 * 加上同步锁后在多线程中就不会出现实例化出多个对象的情况
 */
public class Singleton {
    //私有化构造器，不让外部类实例化此类
    private Singleton (){}
    //定义一个对象引用
    private static  Singleton instance;
    public static Singleton getInstance(){
        if(instance == null){ //这个判断是为了提高效率，如果instance已经实例化就不用加同步锁
            synchronized (Singleton.class){
                if(instance == null){ //如果instance为空时就实例化对象
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
> 小结：上边这种实现方式就很好的解决的多线程中的安全问题，因为在同一时刻只能有一个线程拥有同步锁，其他线程只能等待本线程执行完后才能拥有同步锁，这样就不会出现同时创建出多个实例的情况。另外上面这个实现中加了两次判断，其中外边的if判断是为了提高效率，因为创建同步锁的过程开销很大，加上判断后就只在instance还没有实例化的时候才会创建同步锁，以后的每次访问就不需要创建同步锁。

## 4.“懒汉式”单例模式的Java实现（静态内部类，线程安全） ##
```java
/**
 * 描述：懒汉式的单例模式实现三
 * 利用了classloader的机制来保证初始化instance时只有一个线程，
 * 所以也是线程安全的，同时没有性能损耗
 */
public class Singleton {
    //私有化构造器，不让外部类实例化此类
    private Singleton (){}
    private static final Singleton getInstace (){
        return LayzyHolder.INSTANCE;
    }
    //静态内部类
    private static class LayzyHolder{
        private static final Singleton INSTANCE = new Singleton();
    }
}
```
> 小结：这种比上面2、3都好一些，既实现了线程安全，又避免了同步带来的性能影响。

## “饿汉式”和“懒汉式”的区别： ##
1. 线程安全：
饿汉式天生就是线程安全的，可直接在多线程中使用
懒汉式本身是非线程安全的，但可以使用上边实现方式3达到线程安全的目的

2. 资源加载和性能：
饿汉式在类创建的同时就实例化一个静态对象出来，不管之后会不会使用这个单例，都会占据一定的内存，但是相应的，在第一次调用时速度也会更快，因为其资源已经初始化完成。
而懒汉式顾名思义，会延迟加载，在第一次使用该单例的时候才会实例化对象出来，第一次调用时要做初始化，如果要做的工作比较多，性能上会有些延迟，之后就和饿汉式一样了。