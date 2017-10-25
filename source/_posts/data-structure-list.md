---
layout: post
title: 数据结构---链表
date: 2017-09-16 9:00
comments: true
categories: 数据结构
tags: 
	- 单链表
	- 链表反转
---
**本文主要内容：**
- 链表定义
- 链表结构
- 单链表的实现代码
- 单链表反转
<!-- more -->

# 1.什么是链表？ #
> 链表是线性表的链式存储的实现（物理顺序可以是任意的，不一定要与逻辑顺序一致）。线性表是指存储相同类型的一组数据，除了第一个和最后一个元素外，每一个元素都有它的一个直接前驱和直接后继，第一个元素有一个直接后继，最后一个元素有一个直接前驱。链表基本元素是结点，它里面包含数据域与指针域，结点之间用指针连接起来，形成一个简单的链。

# 2.链表结构 #
链表的每个结点中只包含一个指针域，单链表中每个结点结构：
![单链表中结点的结构](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170918091301.png)
**1. 头指针和头结点:**
单链表有带头结点结构和不带头结点结构两种。
> 链表中第一个结点存储位置叫做头指针，如果链表有头结点，那么头指针就是指向头结点的指针，如果没有头结点，那么头指针就是指向首元结点（第一个元素结点）

如下图所示：
![链表结构图](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170918095302.png)
不带头结点的单链表结构如下：
![不带头结点的单链表结构](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170918100203.jpg)
带头结点的单链表结构如下：
![带头结点的单链表结构](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170918100404.jpg)
**2. 不带头结点的单链表的插入操作：**
![链表插入操作](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170918102305.png)
上图中，是对不带头结点的单链表的插入结点操作，如果是在非首元结点前插入，只需将a(i-1)的指针域指向s结点，然后将s结点的指针域t指向a(i)结点就实现了插入操作；如果是在首元结点前插入，头指针head就需要指向s结点，然后将s结点的指针域指向a(0)结点。所以需要对这两种情况分别操作
**3. 带头结点的单链表的插入操作：**
![链表插入操作](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170918105806.png)
上图中，如果采用带头结点的单链表结构，算法实现时，p指向头结点，改变的是p指针的next指针的值（改变头结点的指针域），而头指针head的值不变。
因此，算法实现方法比较简单，其操作与对其它结点的操作统一。

# 3.单链表的Java代码实现： #
1. 结点类：
单链表是由一个一个结点组成的，因此要设计链表类必须先设计结点类Node.java

```java
/**
 * 作者：陈德华
 * 时间：2017/9/15 13:56
 * 描述：线性链表中的节点类
 */
public class Node {
    Object element; //数据域
    Node next;      //指针域

    //头结点构造器，只需要指定指针域即可
    public Node(Node nextVal){
        this.next = nextVal;
    }

    //非头节点构造器
    public Node(Object obj,Node nextVal){
        this.element = obj;
        this.next = nextVal;
    }


    /**
     * 获得当前节点的数据
     * @return 返回当前节点的数据域
     */
    public Object getElement(){
        return this.element;
    }

    /**
     * 获得当前节点的指针域
     * @return 返回当前节点的指针域
     */
    public Node getNext(){
        return this.next;
    }

    /**
     * 设置当前节点的数据域
     * @param element 数据元素
     */
    public void setElement(Object element) {
        this.element = element;
    }

    /**
     * 设置当前节点的指针域
     * @param next 指针元素
     */
    public void setNext(Node next) {
        this.next = next;
    }

    @Override
    public String toString() {
        return "Node{" +
                "element=" + element +
                ", next=" + next +
                '}';
    }
}
```

单链表类：LinkList.java

```java
/**
 * 作者：陈德华
 * 时间：2017/9/15 14:05
 * 描述：
 */
public class LinkList {
    private Node head;     //头结点
    private Node current;  //当前节点位置
    private int size;      //节点个数

    //初始化一个空链表
    public LinkList(){
        //初始化头结点，让头指针指向头结点，并且让当前节点等于头结点
        this.head = new Node(null);
        this.current = this.head;
        this.size = 0;  //初始化链表长度为0
    }

    //定位函数
    public void index(int index) throws Exception{
        if(index<-1 || index>size-1){
            throw new Exception("参数错误");
        }

        //说明在头结点之后操作，因为第一个元素下标是0，头结点下标就是-1了
        if(index==-1){
            return;
        }

        current = head.next;
        int j = 0;  //循环变量
        while (current!= null&&j<index){
            current = current.next;
            j++;
        }
    }



    /**
     * 获得线性表长度
     */
    public int size() {
        return this.size;
    }

    /**
     * 判断线性表是否为空
     *
     * @return 返回布尔值
     */
    public boolean isEmpty() {
        return this.size==0;
    }

    /**
     * 插入元素到指定索引位置
     *
     * @param index 指定索引位置
     * @param obj   插入的元素
     * @throws Exception 抛出异常
     */
    public void insert(int index, Object obj) throws Exception {
        if (index<0 || index >size){
            throw new  Exception("参数错误");
        }
        index(index-1);  //定位到要操作节点的前一个结点对象
        current.setNext(new Node(obj,current.next));
        size++;
    }

    /**
     * 删除指定索引位置元素
     *
     * @param index 指定索引位置
     * @throws Exception 抛出异常
     */
    public void delete(int index) throws Exception {
        if (isEmpty()){
            throw new Exception("链表为空，无法删除");
        }
        if(index<0||index>size-1){
            throw new Exception("参数错误");
        }
        index(index-1);  //定位到要操作结点的前一个对象
        current.setNext(current.next.next);
        size--;
    }

    /**
     * 获得指定位置的元素
     *
     * @param index 指定的索引位置
     * @return 返回指定索引位置的元素
     */
    public Object get(int index) throws Exception{
        if(index<-1||index>size-1){
            throw new Exception("参数非法");
        }
        index(index);
        return current.getElement();
    }
}
```

# 4.改进后的单链表实现： #
上面的程序中Node是一个单独的类，那么这样的类是可以被用户直接使用的，但是这个类由用户直接去使用，没有任何的意义，即：Node这个类有用，但是不能让用户去用，只能让LinkList类去调用，内部类Node中完成。
于是，我们需要把Node类定义为内部类，并且在Node类中去完成addNode和delNote等操作。使用内部类的最大好处是可以和外部类进行私有操作的互相访问。
<label style="color:blue">注：内部类访问的特点是：内部类可以直接访问外部类的成员，包括私有；外部类要访问内部类的成员，必须先创建对象。</label>

```java
/**
 * 作者：陈德华
 * 时间：2017/9/15 15:42
 * 描述：
 */
public class LinkList {
    private Node root;  //定义一个根节点
    private int size;   //定义链表大小
    private int index;  //定义索引

    private String [] retData ; //返回数组

    /**
     * 新增元素
     * @param s 需要新增的元素
     * @return 返回新增结果，成功时返回true，否则返回false
     */
    public boolean add(String s){
        if (s==null){
            return false; //如果字符串为空就返回false。增加失败
        }
        Node newNode = new Node(s);  //创建一个新节点
        if(root==null){
            root = newNode; //新增第一个节点时root肯定为空，将root指向newNode，即第一个变为根节点
        }else{
            root.addNode(newNode);
        }
        this.size ++;   //新增元素后将size加1
        return true;
    }

    /**
     * 新增一组元素
     * @param str 字符串数组
     * @return 返回新增结果
     */
    public boolean addAll(String str[]){
        for (int i=0;i<str.length;i++){
            if (!this.add(str[i])){  //只要有一次添加不成功就是失败
                return false;
            }
        }
        return true;
    }

    /**
     * 得到链表的大小
     * @return 返回链表的大小
     */
    public int size(){
        return this.size;
    }

    /**
     * 判断链表是否为空
     * @return 为空返回true
     */
    public boolean isEmpty(){
        return this.size==0;
    }

    /**
     * 查询数据是否存在
     * @param str 需要查询的数据
     * @return 返回查询结果
     */
    public boolean contains(String str){
        if(this.root==null||str==null){
            return false;   //如果根节点为空或查找的数据为空就不需要查找了，直接返回false
        }
        return this.root.containNode(str);
    }

    /**
     * 删除元素
     * @param str 需要删除的数据
     */
    public boolean remove(String str){
        //要删除的字符串不存在直接返回false
        if(!this.contains(str)){
            System.out.println(str+"不存在");
            return false;
        }

        if(this.root !=null){
            if(this.root.data.equals(str)){
                this.root = this.root.next;   //让根节点的下一个节点等于根节点就相当于删除了根节点
            }else {
                this.root.removeNode(str);
            }
        }
        this.size --;    //删除完后size减1
        return true;
    }

    /**
     * 链表反转
     */
    public void reverseList(){
        Node temp ;
        Node current = this.root;   //将current指向root

        while (current.next!=null){
            temp = current.next;
            current.next = current.next.next;
            temp.next = root;
            root = temp;
        }
    }

    /**
     * 输出所有节点
     */
    public void print(){
        if(this.root!=null){
            System.out.print(this.root.data);
            this.root.printNode();
            System.out.println();
        }
    }


    /**
     * 取到全部数据
     * @return
     */
    public String [] toArray(){
        if (this.size==0){
            return null;
        }else {
            this.index = 0;
            this.retData = new String[this.size];  //开辟数组大小
            this.root.toArrayNode();
            return retData;

        }
    }

    /**
     * 取到指定索引位置的元素
     * @param index 指定索引值
     * @return 返回元素
     */
    public String get(int index){
        if(index>this.size){
            return null;
        }
        this.index = 0;
        return this.root.getNode(index);
    }

    /**
     * 清空链表
     */
    public void clear(){
        this.root = null;   //将根节点置为空就将链表断开了
        this.size = 0;
    }
    

    /**
     * 节点类
     */
    class Node{
        private String data;    //数据域
        private Node next;      //指针域

        public Node(String data){
            this.data = data;
        }

        /**
         * 新增结点,这里利用递归，直到this.next为空时停止递归，即当前结点后边可以新增结点时，就在当前结点后边加上新节点
         * @param newNode 结点对象
         */
        public void addNode(Node newNode){
            if(this.next==null){       // 递归的出口：如果当前节点之后没有节点，说明我可以在这个节点后面添加新节点
                this.next = newNode;
            }else {
                this.next.addNode(newNode);
            }
        }

        /**
         * 判断节点是否存在
         * @param str 字符串
         * @return 返回查询结果
         */
        public boolean containNode(String str){
            if(this.data.equals(str)){
                return true;
            }else{
                if(this.next != null){  //有后续节点，继续查找
                    return this.next.containNode(str);
                }else{  //没有后续节点
                    return false;
                }
            }
        }

        /**
         * 删除节点
         * @param str 需要删除的字符串
         */
        public void removeNode(String str){
            if(this.next!=null){
                if(this.next.data.equals(str)){
                    this.next = this.next.next;
                }else {
                    this.next.removeNode(str);
                }
            }
        }

        //输出所有节点
        public void printNode(){
            if(this.next!=null){
                System.out.print("-->"+this.next.data);
                this.next.printNode();
            }
        }

        /**
         * 获取全部数据
         */
        public void toArrayNode(){
            LinkList1.this.retData[LinkList1.this.index++] = this.data;
            if(this.next!=null){
                this.next.toArrayNode();
            }
        }

        /**
         * 取到指定索引位置的节点
         * @param index 索引位置
         * @return 返回指定索位置指定额节点
         */
        public String getNode(int index){
            if(LinkList1.this.index ++ == index){
                return this.data;
            }else {
                return this.next.getNode(index);
            }
        }


    }

}
```