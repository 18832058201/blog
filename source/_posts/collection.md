---
layout: post
title: Java集合详解（一）
date: 2017-09-23 09:30
comments: true
categories: 集合
tags: 
	- Set集合
---

Java集合类是一种特别有用的工具类，它可以用于存储数量不等的多个对象，并可以实现常用数据结构，如栈、队列等。除此之外还可用于保存具有映射关系的关联数组。Java集合大致上可分为：`Set`、`List`和`Map`三种体系，其中<label style='color:blue'>Set代表无序、不可重复的集合；List代表有序、重复的集合；而Map则代表具有映射关系的集合。从JDK1.5以后，Java又增加了Queue体系集合，代表一种队列集合实现。</label>

<!-- more -->

----------
# Java集合的概述 #
所有的集合类都位于`java.util包`下

集合类和数组不一样，<label style='color:blue'>数组元素既可以是基本数据类型的值，也可以是对象</label>（实际上保存的是对象的引用变量）;<label style='color:blue'>而集合里只能保存对象</label>(实际上也是保存对象的引用变量，但通常习惯上认为集合里保存的是对象)。

Java的集合类主要由两个接口派生而出：Collection 和Map，Collection 和Map是Java集合矿建的根接口。
![集合框架图](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170923100601.png)
其中Set和List接口是Collection接口派生的两个子接口，它们分别代表了无序集合和有序集合；Queue是Java提供的队列实现，有点类似于List。

下图是Map体系的继承树，所有的Map实现类用于保存具有映射关系的数据
![Map体系继承树](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170923101202.png)
Map保存的每项数据都是key-value对，也就是key和value两个值组成，Map里的key是不可重复的，key用于标识集合里每项数据，可以通过key来获取Map中的数据

我们可以把Java集合分成三大类，其中Set集合类似于一个罐子，把一个对象添加到Set集合时，Set集合无法记住添加这个元素的顺序，所以Set集合里的元素不能重复；List集合非常像一个数组，它可以记住每次添加元素的顺序，只是List的长度可变。Map集合也像一个罐子，只是它里面的每项数据都由两个值组成。
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170923102003.png)
从图中可以看出，如果访问List集合中的元素，可以直接根据索引来访问；如果需要访问Map集合中的元素，可以根据每项元素的key来访问其value；如果希望访问Set集合中的元素，则只能根据元素本身来访问（这也是Set集合里元素不允许重复的原因）。

<label style='color:blue'>对于Set、List和Map三种集合，最常用的实现类分别是HashSet、ArrayList和HashMap</label>

# Collection和Iterator接口 #
Collection接口是List、Set和Queue接口的父接口，该接口里定义的方法既可用于操作Set集合，也可用于操作List集合和Queue集合。

- <label style='color:blue'>boolean add(Object o)</label>:该方法用于向集合里添加一个元素。如果集合对象呗添加操作改变了则返回true
- boolean addAll(Collection c):该方法把集合c里的所有元素添加到指定集合里。如果集合对象被添加操作改变了则返回true
- void clear():清除集合里的所有元素，集合长度将变为0
- boolean contains(Object o):返回集合里是否包含指定元素
- boolean containsAll(Collection c):返回集合里是否包含集合c里的所有元素
- <label style='color:blue'>boolean isEmpty()</label>:返回集合是否为空。当集合长度为0时返回true，否则返回false
- <label style='color:blue'>Iterator iterator()</label>：返回一个Iterator对象，用于遍历集合里的元素
- <label style='color:blue'>boolean remove(Object o)</label>:删除集合中指定元素o，当集合中包含了一个或多个元素o时，这些元素都将被删除，该方法返回true
- boolean removeAll(Collection c):从集合中删除集合c里包含的所有元素，如果删除了一个或一个以上的元素，该方法返回true
- boolean retainAll(Collection c):从集合中删除集合c里不包含的所有元素，如果该操作改变了调用该方法的集合，该方法返回true
- <label style='color:blue'>int size()</label>:返回该集合里元素的个数
- Object[] toArray:该方法把集合转换成一个数组，所有集合元素编程对应的数组元素

## 使用Iterator接口遍历集合元素 ##
<label style='color:blue'>Iterator主要用于遍历（即迭代访问）Collection集合中的元素，Iterator也比成为迭代器</label>

Iterator接口隐藏了各种Collection实现类的底层细节，向应用程序提供了遍历Collection集合元素的统一编程接口，Iterator几口里定义了如下三个方法：
- boolean hasNext():如果被迭代的集合元素还没有被遍历，则返回true
- Object next():返回集合里下一个元素
- void remove():删除集合里上一次next方法返回的元素

程序示例，通过Iterator来遍历集合的元素：

```java
public class TestIterator {
    public static void main(String[] args) {
        //创建一个集合
        Collection books = new HashSet();
        books.add("java疯狂讲义");
        books.add("深入理解Java虚拟机");
        books.add("剑指offer");

        //获取books集合对应的迭代器
        Iterator it = books.iterator();
        while(it.hasNext()){
            String book = (String) it.next();
            if ("java疯狂讲义".equals(book)){
                it.remove(); //删除当前迭代的对象
            }
            //对book变量赋值，不会改变集合元素本身
			book = "测试字符串";
        }
    }
}
```
注意：Iterator必须依附于Collection对象，有一个Iterator对象，则必然有一个与之关联的Collection对象，Iterator提供了2个方法来迭代访问Collection集合里的元素，并可通过remove方法来删除集合中上一次next方法返回的集合元素

当使用Iterator来迭代访问Collection集合元素时，Collection集合里的元素不能被改变，只有通过Iterator的remove方法来删除上一次next方法返回的集合元素才可以。否则将会引发`java.util.ConcurrentModificationException`异常。

Iterator迭代器采用的是快速失败（fail-fast）机制，一旦在迭代过程中检测到该集合已经被修改（通常是程序中其他线程修改），程序立即引发`ConcurrentModificationException`异常，而不是显示修改后的结果，这样可以避免共享资源而引发的潜在问题。

## 使用foreach循环遍历集合元素 ##
除了可以使用Iterator接口迭代访问Collection集合里的元素之外，使用JDK1.5提供的foreach循环来迭代访问元素更加便捷

```java
public class TestForeach {
    public static void main(String[] args) {
        //创建一个集合
        Collection books = new HashSet();
        books.add("java疯狂讲义");
        books.add("深入理解Java虚拟机");
        books.add("剑指offer");

        for(Object obj:books){
            //此处的book变量也不是集合元素本身
            String book = (String) obj;
            if("java疯狂讲义".equals(book)){
                //下面代码会引发ConcurrentModificationException异常
                books.remove(book);
            }
        }
    }
}
```
foreach循环中的迭代变量也不是集合元素本身，系统只是依次把集合的值赋给迭代变量，因此在foreach循环中修改迭代变量的值也没有任何实际意义。同样，当使用foreach循环迭代访问集合元素时，该集合也不能被改变，否则将引发ConcurrentModificationException异常

# Set接口 #
Set集合与Collection基本上完全一样，它没有提供额外的方法。实际上Set就是Collection，只是行为不同（<label style='color:blue'>Set不允许包含重复元素</label>）.

Set判断两个对象相同不是使用==运算符，而是根据equals方法（“==”比较的是对象的地址，equals方法比较的是对象的内容）。也就是说，如果只要两个对象用equals方法比较返回true，Set就不会接受这两个对象；反之，只要两个对象用equals方法比较返回false，Set就会接受这两个对象。
```java
public class TestSet {
    public static void main(String[] args) {
        //创建一个set集合
        Set books = new HashSet();
        //添加一个字符串对象
        books.add(new String("深入理解java虚拟机"));

        //再次添加一个字符串对象,由于用equals比较字符串对象，所以返回false
        boolean result = books.add(new String("深入理解java虚拟机"));
        //下面输出只有一个元素
        System.out.println(books);
    }
}
```

<label style='color:red'>equals方法和“==”的区别 </label>
- Java 语言里的 equals方法其实是交给开发者去覆写的，让开发者自己去定义满足什么条件的两个Object是equal的，String类中的equals方法见下边源码
- “==”比较基本数据类型时，比较的是数据的值是否相等；比较对象时，比较的是对象的地址是否相等，即引用是否指向同一个对象。

String类中重写了equals方法，见源码：
```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }

```
可以看出，String中的equals方法先是判断两个对象的引用（地址）是否相同，如果不相同再比较String的value是否相同。

## HashSet类 ##

HashSet是Set接口的典型实现，大多数时候使用Set集合时就是使用这个实现类。HashSet按Hash算法来存储集合中的元素，因此具有很好的存取和查找性能。
HashSet具有以下的特点：
- 不能保证元素的排列顺序，顺序有可能发生变化，即HashSet是无序的。
- HashSet不是同步的，如果有多个线程同时访问一个HashSet，必须通过代码来保证其同步
- 集合元素值可以是null

当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据该hashcode值来决定该对象在HashSet中存储位置。如果有两个元素通过equals方法比较返回true，但它们的hashcode()方法返回值不相等，HashSet将会把它们存储在不同位置，也就可以添加成功。

<label style='color:blue'>简单地说，HashSet集合判断两个元素相等的标准是两个对象通过equals方法比较相等，并且两个对象的hashco()方法返回值也相等。</label>

<label style='color:red'>注意：如果需要某个类的对象保存到HashSet集合中，重写这个类的equals()方法和Hashcode方法时，应该尽量保证两个对象通过equals比较返回true时，他们的hashcode方法返回值也相等。</label>


<label style='color:red'>
**hashCode 方法对于HashSet的作用是什么？**</label>
我们先要理解hash（也被翻译为哈希、散列）算法的功能：<label style='color:blue'>他能保证通过一个对象快速查找到另一个对象。hash算法的价值在于速度，它可以保证查询得到快速执行</label>。当需要查询集合中某个元素时，hash算法可以直接根据元素的值得到该元素保存在何处，从而可以让程序快速找打该元素。为了理解这个概念，我们先看数组：数组可以包含多个元素，每个元素也有索引，如果需要访问某个数组元素，只需提供该元素的索引，该索引即指出了该元素在数组内存区里的存储位置。
表面上看起来，HashSet集合里的元素都没有索引，实际上当程序向HashSet集合中添加元素时，HashSet会根据该元素的hashcode值来决定它的存储位置---也就是说，每个元素的hashcode就是它的索引。

> HashSet中每个能存储元素的“槽位(slot)”通常称为“桶(bucket)”，如果有多个元素的hashCode相同，但它们通过equals方法比较返回false，就需要在一个“桶”里放多个元素，从而导致性能下降。

<label style="color:blue">hashCode()方法的基本规则：
- 当当个对象通过equals方法比较返回true时，这两个对象的hashCode应该相等。
- 对象中作用equals比较标准的属相，都应该用来计算hashCode值。</label>

<label style="color:red">注意：当向HashSet中添加可变对象时，必须十分小心，如果修改HashSet集合中的对象，有可能导致该对象与集合中其他对象相等，从而导致HashSet无法准确访问该对象。</label>

HashSet还有一个子类LinkedHashSet，LinkedHashSet集合也是根据元素hashCode值类决定元素存储位置，但它同时使用链表维护元素的次序，这样使得元素看起来是已插入的顺序保存的。也就是说，当遍历LinkedHashSet集合里元素时，HashSet将会按元素的添加顺序来访问集合里的元素。
LinkedHashSet需要维护元素的插入顺序，因此性能略低于HashSet的性能，但在迭代访问Set里的全部元素时将有很好的性能，因为他以链表来维护内部顺序。

## TreeSet类 ##

TreeSet类是SortedSet接口的唯一实现，正如SortedSet名字所暗示的，TreeSet可以确保集合元素处于排序状态。

TreeSet并不是根据元素的插入顺序进行排序，而是根据元素实际值来进行排序的

与HashSet集合采用hash算法来决定元素的存储位置不同，TreeSet采用红黑树的数据结构对元素进行排序。TreeSet支持两种排序方法：自然排序和定制排序。默认情况下，TreeSet采用自然排序。

**自然排序**
TreeSet会调用集合元素的compareTo(Object obj)方法来比较元素之间大小关系，然后将集合元素按升序排列，这种方式就是自然排序。
java提供了一个Comparable接口，该接口里定义了一个compareTo(Object obj)方法，该方法返回一个整数值，实现该接口的类必须实现该方法，实现了该接口的对象就可以比较大小。当一个对象调用该方法与另一个对象进行比较，例如obj1.compareTO(obj2),如果该方法返回一个负整数，则表明obj12小与obj2.

<label style="color:red">如果试图把一个对象添加进TreeSet时，该对象的类必须实现Comparable接口，否则程序将会抛出异常</label>

**定制排序**
TreeSet的自然排序是根据集合元素的大小，TreeSet将它们以升序排列。如果需要实现定制排序，例如以降序排列，则可以使用Comparator接口的帮助，该接口里包含一个Int compareTo(T o1,T o2)方法，该方法用于比较o1和o2的大小：如果该方法返回正整数，则表明o1大于o2；如果该方法返回0，则表明o1等于o2；如果该方法返回负整数，则表明o1小与o2

程序示例：
```java
public class Main {
    public static void main(String[] args) {
        TreeSet ts = new TreeSet(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                M m1 = (M) o1;
                M m2 = (M) o2;
                if (m1.age > m2.age){
                    return -1;
                }else if (m1.age == m2.age){
                    return 0;
                }else {
                    return 1;
                }
            }
        });

        ts.add(new M(5));
        ts.add(new M(-3));
        ts.add(new M(9));
        System.out.println(ts);
    }
}

class M{
    int age;
    public M(int age){
        this.age = age;
    }
    public String toString(){
        return "M对象（age:"+age+"）";
    }
}
```
运行结果如下：
```java
[M对象（age:9）, M对象（age:5）, M对象（age:-3）]
```
<label style="color:red">注意：当通过Comparator对象来实现TreeSet的定制排序时，依然不可以向TreeSet中添加类型不同的对象，否则依然会引发ClassCastException异常。使用定制排序时，TreeSet对集合元素排序时不管集合元素本身的大小，而是由Comparator对负责集合元素的排序规则。</label>



