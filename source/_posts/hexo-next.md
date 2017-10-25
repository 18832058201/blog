---
title: Hexo + Next 打造个性化博客
date: 2017-08-11 22:21:52
categories: hexo
tags: 
   - Hexo主题
---
<center style="font-size:24px">Next ---“精于心，简于形”</center>
尝试过好几个主题，最终还是选定这个评分最高的Next主题，主要是他的简洁大方吸引了我，非常符合这个主题的宗旨“精于心，简于形”，本文主要记录我使用这个主题搭建博客的一个历程，记录的可能不全，可参考Next主题官方文档[Next主题官方说明](http://theme-next.iissnan.com/)
<!-- more -->
# 下载Next主题 #
- 方式一：直接去next主题的代码托管平台github上下载：[Next主题下载地址](https://github.com/iissnan/hexo-theme-next/releases)
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812225244.png)
选择对应的版本号下载即可，下载后将主题解压然后将主题扔到你博客根目录下themes文件夹中
- 方式二：用Git 命令克隆
在终端窗口定位到你的站点根目录下，然后执行Git命令,克隆下来的是最新版本的主题
```bash
cd your-hexo-site
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
<font color= "red">注意一定得在站点根目录下执行该命令</font>，执行完该命令后打开themes文件夹即可看到刚才克隆下来的主题了

# 启用Next主题 #
- 打开站点根目录下的`_config.yml`(站点配置文件)
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812230756.png)
- 找到theme的位置，修改为刚才下载的主题的文件夹名称，<font color="red">注意所有的属性和值之间要加空格，否则可能会不识别</font>
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812231001.png)
- 修改后执行以下命令然后浏览器查看效果，是不是已经应用上next主题
```bash
hexo clan
hexo g
hexo s
```
这里顺便讲解一下hexo的常用命令：
> npm install hexo -g #安装Hexo
> npm update hexo -g #升级 
> hexo init #初始化博客

命令简写

> hexo n "我的博客" == hexo new "我的博客" #新建文章
> hexo g == hexo generate #生成
> hexo s == hexo server #启动服务预览
> hexo d == hexo deploy #部署

> hexo server #Hexo会监视文件变动并自动更新，无须重启服务器
> hexo server -s #静态模式
> hexo server -p 5000 #更改端口
> hexo server -i 192.168.1.1 #自定义 IP
> hexo clean #清除缓存，若是网页正常情况下可以忽略这条命令

# 配置站点配置文件 #
这部分网站名称等配置，暂时只需要配置这个几个
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812233154.png)
# 配置主题的配置文件 #
主题配置文件位于主题文件夹中`_config.yml`,这里设置主题相关的配置
## 主题设定 ##
next提供了三种主题方案可以更换
![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170812233959.png)
## 菜单设置 ##
这个就建议参考官方说明[Next主题官方说明](http://theme-next.iissnan.com/)，讲的比较详细
# 集成第三方服务 #
- 第三方评论系统，我采用的是友言，[友言网址](http://www.uyan.cc)主要是这个注册流程比较方便，注册后进入后台管理，直接获取代码，然后将id复制下来粘贴到对应的地方，畅言需要网站备案号什么的才能获取id，如果有网站备案号可以采用畅言评论
- 网站分析，建议用腾讯分析，百度分析同样需要网站的备案信息什么的，网站分析可以让你看到网站的访问情况，时段分布，地狱分布等信息
- 网站统计，可以采用不蒜子统计 ，直接修改相关配置即可，不需要注册账号获取id等操作
- 本地搜索服务
我采用的是Local Search，同样是操作比较方便
1. 安装 hexo-generator-searchdb，在站点的根目录下执行以下命令：
 `$ npm install hexo-generator-searchdb --save`
2. 编辑 站点配置文件，新增以下内容到任意位置：
```bash
    search:
      path: search.xml
      field: post
      format: html
      limit: 10000
```
3. 编辑 主题配置文件，启用本地搜索功能：
```bash
    # Local search
    local_search:
      enable: true
```
# 其他个性化设置 #
1. 去掉文章中图片的边框
进入到文件：
..\source\css\_common\components\post\post-expand.styl
找到51行位置，修改如下代码：
```bash
 img {
    box-sizing: border-box;
    margin: auto;
    padding: 3px;
    //border: 1px solid $gray-lighter;
	//修改后的边框样式（不显示边框）
	border: none;
  }
```
