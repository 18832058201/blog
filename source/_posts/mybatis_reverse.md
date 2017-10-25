---
layout: post
title: mybatis逆向工程
date: 2017-08-12 22:00
comments: true
categories: 框架
tags: 
	- mybatis
---

# 一、mybatis逆向工程搭建 #
github下载地址：[mybatis逆向工程](https://github.com/18832058201/generatorSqlmap)
## 1.导入jar包依赖 ##
普通Java工程需导入以下jar包
![mybatis逆向工程需导入的jar包](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170811223630.png)
<!-- more -->
maven工程需导入如下依赖，数据库驱动和mybatis核心jar依赖在此省略
```java
<!--mybatis逆向工程jar包-->
    <dependency>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-core</artifactId>
      <version>1.3.2</version>
	  <!--开发时用,这个可以不指定-->
      <scope>test</scope>
    </dependency>
```
## 2.添加generatorConfig.xml文件 ##
内容如下：
```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="testTables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否，建议设置为true，生成的mybatis的注释没有意义 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <!--MySQL数据库连接配置-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://127.0.0.1:3306/keyan" userId="root"
                        password="admin">
        </jdbcConnection>
        <!--oracle数据库连接配置-->
        <!-- <jdbcConnection driverClass="oracle.jdbc.OracleDriver"
            connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:yycg"
            userId="yycg"
            password="yycg">
        </jdbcConnection> -->
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.hebeu.keyan.po"
                            targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="com.hebeu.keyan.dao"
                         targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.hebeu.keyan.dao"
                             targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>

        <!-- 指定数据库表 -->
        <!--<table tableName="sys_user" domainObjectName="User"/>-->
        <!--<table tableName="sys_role" domainObjectName="Role"/>-->
        <!--<table tableName="sys_permission" domainObjectName="Permission"/>-->
        <!--<table tableName="sys_user_role" domainObjectName="UserRole"/>-->

        <!-- 有些表的字段需要指定java类型
         <table schema="" tableName="">
            <columnOverride column="" javaType="" />
        </table> -->
    </context>
</generatorConfiguration>
```

## 3.执行程序 ##
代码如下：
```java
	public class GeneratorSqlmap {
		private void generator() throws Exception{
	
			List<String> warnings = new ArrayList<>();
			/* 指定 逆向工程配置文件 */
			File configFile = new File("generatorConfig.xml");
			ConfigurationParser cp = new ConfigurationParser(warnings);
			Configuration config = cp.parseConfiguration(configFile);
			DefaultShellCallback callback = new DefaultShellCallback(true);
			MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
					callback, warnings);
			myBatisGenerator.generate(null);
	
		} 
		public static void main(String[] args) throws Exception {
			try {
				GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
				generatorSqlmap.generator();
			} catch (Exception e) {
				e.printStackTrace();
			}
			
		}
	}
```
到此mybatis逆向工程就搭建好了，下面讲解mybatis逆向工程的使用

# 二、mybatis逆向工程的使用 #

## 1.mybatis逆向工程中生成的代码结构  ##

 ![mybatis中生成的代码结构](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20170811234227.png)

*其中dao中是对应的mapper接口和mapper映射
po中一个是实体类，一个是查询条件类，通过这个类中封装的查询条件可以很方便的进行增删改查操作，其中增删改基本上可以不用自己写sql语句，对查询有特殊要求需要自己写*

## 2.查询方法（这里只讲查询，增删改方法使用类似） ##

```java
    //通过Criteria查询对象查询的四个步骤：

    //1.创建一个RewClass实体类的一个Example类
    RewClassExample rewClassExample = new RewClassExample();
    //2.通过example类中的createCriteria()创建一个criteria对象
    RewClassExample.Criteria criteria = rewClassExample.createCriteria();
    //3.给criteria对象增加查询条件
    criteria.andRewClassIdIsNotNull();  //创建id列不为空的条件，即可表示查询所有的值
    //4.调selectByExample方法查询满足条件的结果集存放到集合中
    List<RewClass> rewClassList = rewClassMapper.selectByExample(rewClassExample);
```