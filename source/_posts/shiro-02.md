---
layout: post
title: shiro学习（二）--- shiro介绍
date: 2017-08-21 20:00
comments: true
categories: 框架
tags: 
	- shiro
	- 权限管理
---

# shiro介绍 #
## 什么是shiro ##
> Shiro是apache旗下一个开源框架，它将软件系统的安全认证相关的功能抽取出来，实现用户身份认证，权限授权、密码加密、会话管理等功能，组成了一个通用的安全认证框架。

<!-- more -->
## 为什么要学shiro？ ##
> 既然shiro将安全认证相关的功能抽取出来组成一个框架，使用shiro就可以非常快速的完成认证、授权等功能的开发，降低系统成本。
shiro使用广泛，shiro可以运行在web应用，非web应用，集群分布式应用中越来越多的用户开始使用shiro。
java领域中spring security(原名Acegi)也是一个开源的权限管理框架，但是spring security依赖spring运行，而shiro就相对独立，最主要是因为shiro使用简单、灵活，所以现在越来越多的用户选择shiro。
