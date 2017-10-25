---
layout: post
title: Hello World
date: 2017-08-10 16:00
categories: hexo
tags: 
   - hexo搭建
---
&emsp;学习一门新的语言都是先从Hello world 入手，本博客也将带着你一步步打开博客世界的新大门，Say Hello world！

# 搭建步骤： #
1. 注册github账号（如果已注册可以省略此步骤）
2. 创建github仓库
2. 安装git
3. 配置本地git环境并建立与远程github的联系
3. 安装Node.js (hexo框架是建立在node.js上的)
4. 安装Hexo
5. 生成本地博客网站
7. 发布博客网站到Github page上

<!-- more -->
网上好多教程都从注册域名开始，个人觉得搭建个人博客不是十分必要，并且域名备案是一件很麻烦的事，审核周期20天左右，如果不觉得麻烦自行百度搜索教程

# 注册github账号 #
登录github官网：[github网址](https://github.com/) &emsp;注册步骤就不详述了
# 创建GitHub仓库 #
注册后登录github创建一个新的Repository，仓库命名为 xxx.github.io （xxx为你的github账号名，github page的固定命名格式），如我的账号为18832058201就命名为18832058201.github.io
![m创建一个新的github仓库](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812174846.png)

# 安装Git #
- 从git官网上下载git：[git官网下载地址](https://git-scm.com/download/win)，根据你的电脑位数选择下载32位还是64位
- 安装git，可参考教程：[Windows上安装git教程](https://jingyan.baidu.com/article/7f766dafba84f04101e1d0b0.html)

# 配置Git并建立与github联系 #
- 安装Git后打开Git bash，桌面没有就去所有程序里找Git文件中的Git bash
- 设置user.name和user.email配置信息：
在bash命令行输入以下内容，输入第一条后回车再输入第二条回车
```bash
	git config --global user.name "你的GitHub用户名"
	git config --global user.email "你的GitHub注册邮箱"
```
- 生成ssh密钥文件：
```bash
	ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```
找到这个文件，打开文件并复制所有内容
![密钥文件](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812182114.png)
- 打开Github网站的密钥设置：[github SSH settings](https://github.com/settings/keys),
- 新建ssh key
![新建SSH key](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812182843.png)
- 检查是否成功建立通信,如果失败重新建一个ssh key
![检查是否成功建立通信](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812183305.png)
这个设置是为了建立本地Git与远程github的一个连接协议，不进行此操作你本地Git无法向远程github提交文件，到此Git的配置就结束了。

# 安装Node.js #
- Node.js下载地址：[Node.js官方下载](https://nodejs.org/en/download/)，Node.js中会包含环境变量和npm，安装后不用配置环境变量，npm是node.js的一个插件安装命令，通过npm可以很方便的安装第三方的js包
- 检查Node.js是否安装成功
![检查node.js是否安装成功](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812183951.png)
如果出现对应的版本号就说明Node.js安装成功

# 安装Hexo #
1. 先建立一个本地文件夹，我这里是在d盘下建立一个blog文件夹
2. 打开dos命令窗口，进入到刚建的文件夹中
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812184820.png)
3. 执行命令安装Hexo
```bash
	npm install -g hexo-cli 
```
# 生成本地博客网站 #
1. 初始化一个blog网站，这个命令的作用是初始化一个博客网站，放在新建的blog文件中,这个过程需要耐心等待一会儿
```bash
	hexo init blog
```
2. 执行命令生成静态网页并启动服务
初始化完成后会发现多了一个blog的文件夹， 先用 cd blog 进入到blog文件夹中，以下命令都是在网站的根目录中执行的。依次执行以下命令
```bash
	hexo g

	hexo s
```
3. 打开浏览器访问：localhost:4000 ,你会发现你的个人博客已经生成了，是不是很简单呢 
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812191705.png)

# 发布博客网站到Github page上 #
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812190716.png)


- 打开配置文件后找到如图所示位置，在内容最后，并修改内容为如下：
 其中仓库地址就是你刚刚在github上建的仓库地址
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812191024.png)
- 安装Git部署插件,必须先安装此插件后才能推送到github上
```bash
npm install hexo-deployer-git --save
```
- 执行推送命令将博客推送到Github上
```bash
	hexo d
```
- 等待推送结束后访问 你的github用户名.github.io 就可以访问你的博客了

需要个性化博客可以参考我的 [Hexo + Next 打造个性化博客](https://18832058201.github.io/2017/08/12/hexo-next/)
