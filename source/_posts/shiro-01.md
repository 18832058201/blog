---
layout: post
title: shiro学习（一）--- 权限管理入门
date: 2017-08-20 20:00
comments: true
categories: 框架
tags: 
	- shiro
	- 权限管理
---
<img src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170814213828.png" style="border:none">
前言：最近在开发一个科研信息管理系统，由于涉及到权限管理，就有幸接触到shiro这个框架，以下便是我学习shiro过程中的一些笔记及学习心得。
<!--more-->

# 权限管理 #

## 什么是权限管理？ ##
> &emsp;&emsp;基本上涉及到用户参与的系统都要进行权限管理，权限管理属于系统安全的范畴，权限管理实现对用户访问系统的控制，按照安全规则或者安全策略控制用户可以访问而且只能访问自己被授权的资源。
&emsp;&emsp;权限管理包括用户身份认证和授权两部分，简称认证授权。对于需要访问控制的资源用户首先经过身份认证，认证通过后用户具有该资源的访问权限方可访问。

## 用户身份认证 ##
1. 概念：
> 身份认证，就是判断一个用户是否为合法用户的处理过程。最常用的简单身份认证方式是系统通过核对用户输入的用户名和口令，看其是否与系统中存储的该用户的用户名和口令一致，来判断用户身份是否正确。
2. 用户名密码身份认证流程
<img src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20171108095003.png" style="border:none">
3. 关键对象
上边的流程图中需要理解以下关键对象：
**Subject：主体**
访问系统的用户，主体可以是用户、程序等，进行认证的都称为主体；
**Principal：身份信息**
是主体（subject）进行身份认证的标识，标识必须具有唯一性，如用户名、手机号、邮箱地址等，一个主体可以有多个身份，但是必须有一个主身份（Primary Principal）。
**credential：凭证信息**
是只有主体自己知道的安全信息，如密码、证书等。
*注：这几个关键对象在后面的程序中还会讲到 *

##  授权  ##
1. 概念：
授权，即访问控制，控制谁能访问哪些资源。主体进行身份认证后需要分配权限（授权）方可访问系统的资源，对于没有权限某的资源是无法访问的。
2. 授权流程图
<img src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20171108094702.png" style="border:none">
3. 关键对象
授权可简单理解为who对what(which)进行How操作：
**Who，即主体（Subject）**，主体需要访问系统中的资源。
**What，即资源（Resource）**，如系统菜单、页面、按钮、类方法、系统商品信息等。资源包括资源类型和资源实例，比如商品信息为资源类型，类型为t01的商品为资源实例，编号为001的商品信息也属于资源实例。
**How，权限/许可（Permission**），规定了主体对资源的操作许可，权限离开资源没有意义，如用户查询权限、用户添加权限、某个类方法的调用权限、编号为001用户的修改权限等，通过权限可知主体对哪些资源都有哪些操作许可。
权限分为粗颗粒和细颗粒，粗颗粒权限是指对资源类型的权限，细颗粒权限是对资源实例的权限。 （下文也会详细讲解这两者的区别）

## 权限模型 ##
通过对上面主体、资源、权限的分析，可以用以下数据模型表示：
> 主体（账号、密码）
> 资源（资源名称、访问地址）
> 权限（权限名称、资源id）
> 角色（角色名称）
> 角色和权限关系（角色id、权限id）
> 主体和角色关系（主体id、角色id）

通常又将资源和权限合并到一起，因为对资源的访问就可以理解为访问的权限，所有二者是可以合并的
总结后可用下图来表示：
<img src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170824230758.png" style="border:none">

## 权限控制 ##
> 用户在访问某个资源时，系统需要对主体进行权限验证，如果有该资源访问的对应权限就放行，如果没有权限就进行拦截。

- 基于角色的访问控制
  RBAC基于角色的访问控制（Role-Based Access Control）是以角色为中心进行访问控制，即在对资源访问时判断主体是否具有某个角色。如：经理需要查看员工工资，访问控制流程图如下：
 <img src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170824230760.png" style="border:none">
上面的流程图用程序代码理解如下：
if(subject.hasRole("总经理角色")){
  查看员工工资
}
缺点：可扩展性差，如果后期需求变为部门经理也能查看员工工资，这时就只能修改代码了
- 基于资源的访问控制
  RBAC基于资源的访问控制（Resource-Based Access Control）是以资源为中心进行访问控制，即在对资源访问时判断主体是否具有访问这个资源对应的权限。同样是上面那个例子，用代码理解如下：
if(subject.hasPermission("查询工资权限")){
  查看员工工资
}
优点：系统设计时定义好查询工资的权限标识，即使查询工资所需要的角色变化为总经理和部门经理也只需要将“查询工资信息权限”添加到“部门经理角色”的权限列表中，判断逻辑不用修改，系统可扩展性强。

## 粗颗粒度和细颗粒度 ##
- 什么是粗颗粒度和细颗粒度
  对资源类型的管理称为粗颗粒度权限管理，即只控制到菜单、按钮、方法，粗粒度的例子比如：用户具有用户管理的权限，具有导出订单明细的权限。对资源实例的控制称为细颗粒度权限管理，即控制到数据级别的权限，比如：用户只允许修改本部门的员工信息，用户只允许导出自己创建的订单明细。
- 如何实现粗颗粒度和细颗粒度
  对于粗颗粒度的权限管理可以很容易做系统架构级别的功能，即系统功能操作使用统一的粗颗粒度的权限管理。
  对于细颗粒度的权限管理不建议做成系统架构级别的功能，因为对数据级别的控制是系统的业务需求，随着业务需求的变更业务功能变化的可能性很大，建议对数据级别的权限控制在业务层个性化开发，比如：用户只允许修改自己创建的商品信息可以在service接口添加校验实现，service接口需要传入当前操作人的标识，与商品信息创建人标识对比，不一致则不允许修改商品信息。

## 基于URL拦截 ##
  基于url拦截是企业中常用的权限管理方法，实现思路是：将系统操作的每个url配置在权限表中，将权限对应到角色，将角色分配给用户，用户访问系统功能通过Filter进行过虑，过虑器获取到用户访问的url，只要访问的url是用户分配角色中的url则放行继续访问。
    如下图：
    ![](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20171107222303.png)
 ### 基于URL拦截代码实现 ###
**数据库表**：创建用户表、角色表、权限表、角色权限关系表、用户角色关系表。
**用户认证授权拦截器（关键代码）**
使用springmvc拦截器对用户访问url进行拦截，如果用户访问的url没有分配权限则跳转到无权操作提示页面（refuse.jsp），本功能也可以使用filter实现。
```java
public class PermissionInterceptor implements HandlerInterceptor {

    // 在进入controller方法之前执行
    // 使用场景：比如身份认证校验拦截，用户权限拦截，如果拦截不放行，controller方法不再执行
    // 进入action方法前要执行
    @Override
    public boolean preHandle(HttpServletRequest request,
            HttpServletResponse response, Object handler) throws Exception {
        // TODO Auto-generated method stub
        // 用户访问地址：
        String url = request.getRequestURI();

        // 校验用户访问是否是公开资源地址(无需认证即可访问)
        List<String> open_urls = ResourcesUtil.gekeyList("anonymousURL");
        // 用户访问的url
        for (String open_url : open_urls) {
            if (url.indexOf(open_url) >= 0) {
                // 如果访问的是公开 地址则放行
                return true;
            }
        }
        //从 session获取用户公共访问地址（认证通过无需分配权限即可访问）
        List<String> common_urls = ResourcesUtil.gekeyList("commonURL");
        // 用户访问的url
        for (String common_url : common_urls) {
            if (url.indexOf(common_url) >= 0) {
                // 如果访问的是公共地址则放行
                return true;
            }
        }
        // 从session获取用户权限信息

        HttpSession session = request.getSession();

        ActiveUser activeUser = (ActiveUser) session.getAttribute("activeUser");

        // 取出session中权限url
        // 获取用户操作权限
        List<SysPermission> permission_list = activeUser.getPermissions();
        // 校验用户访问地址是否在用户权限范围内
        for (SysPermission sysPermission : permission_list) {
            String permission_url = sysPermission.getUrl();
            if (url.contains(permission_url)) {
                return true;
            }
        }

        // 跳转到页面
        request.getRequestDispatcher("/WEB-INF/jsp/refuse.jsp").forward(
                request, response);
        return false;
    }
```
**用户登录**
用户输入用户账号和密码登陆，登陆成功将用户的身份信息（用户账号、密码、权限菜单、权限url等）记入activeUser类，并写入session。
```java
    //用户登陆提交
    @RequestMapping("/loginsubmit")
    public String loginsubmit(HttpSession session,String usercode,String password,String randomcode) throws Exception{

        //校验验证码
        //从session获取正确的验证码
        String validateCode = (String)session.getAttribute("validateCode");
        if(!randomcode.equals(validateCode)){
            //抛出异常：验证码错误
            throw new CustomException("验证码 错误 ！");
        }
        //用户身份认证
        ActiveUser activeUser = sysService.authenticat(usercode, password);
        
        //记录session
        session.setAttribute("activeUser", activeUser);
        
        return "redirect:first.action";
    }
```
登录业务类在此省略……

## 使用权限管理框架实现 ##
 对于权限管理基本上每个系统都有，使用权限管理框架完成权限管理功能的开发可以节省系统开发时间，并且权限管理框架提供了完善的认证和授权功能有利于系统扩展维护，但是学习权限管理框架是需要成本的，所以选择一款简单高效的权限管理框架显得非常重要。下边就将介绍基于shiro权限管理框架来实现权限管理。




