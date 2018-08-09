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

## shiro架构 ##
![shiro架构](http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20171107093301.png)

### Subject ###
   Subject即主体，外部应用与subject进行交互，subject记录了当前操作用户，将用户的概念理解为当前操作的主体，可能是一个通过浏览器请求的用户，也可能是一个运行的程序。
   Subject在shiro中是一个接口，接口中定义了很多认证授相关的方法，外部程序通过subject进行认证授，而subject是通过SecurityManager安全管理器进行认证授权

### SecurityManager ###
SecurityManager即安全管理器，对全部的subject进行安全管理，它是shiro的核心，负责对所有的subject进行安全管理。通过SecurityManager可以完成subject的认证、授权等，实质上SecurityManager是通过Authenticator进行认证，通过Authorizer进行授权，通过SessionManager进行会话管理等。
	SecurityManager是一个接口，继承了Authenticator, Authorizer, SessionManager这三个接口。

### Authenticator ###
Authenticator即认证器，对用户身份进行认证，Authenticator是一个接口，shiro提供ModularRealmAuthenticator实现类，通过ModularRealmAuthenticator基本上可以满足大多数需求，也可以自定义认证器

### Authorizer ###
Authorizer即授权器，用户通过认证器认证通过后，在访问功能时需要通过授权器判断用户是否有此功能的操作权限。

### Realm ###
Realm即领域，相当于DataSource数据源，securityManager进行安全认证需要通过Realm获取用户授权数据，比如：如果用户身份数据在数据库，那么Realm就需要从数据库获取用户身份信息
注意：Realm不只是从数据库中获取用户的身份信息，还有相关的认证授权校验逻辑代码

### SessionManager ###
sessionManger即会话管理，shiro框架定义了一套会话管理器，它不依赖于web容器的session，所以shiro可以使用在非web应用上，也可以将分布式应用的会话集中在一点管理，此特性可以实现单点登录。

### SessionDAO ###
SessionDAO 即会话dao，是对session会话操作的一套接口，比如要将session存储到数据库，可以通过jdbc将会话存储到数据库。

### CacheManager ###
CacheManger即缓存管理，将用户权限数据存储在缓存，这样可以提高性能。

### Cryptography ###
Cryptography即密码管理器，shiro提供了一套加密/解密的组件，方便开发。比如提供常用的散列、加/解密等功能。

## shiro的jar包 ##
将shiro的jar包加入项目中就可以使用shiro提供的功能了。
shiro-core是核心包必须选用，还提供了与web整合的shiro-web、与spring整合的shiro-spring、与任务调度quartz整合的shiro-quartz等，下边是shiro各jar包的maven依赖。
```java
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-core</artifactId>
	<version>1.2.3</version>
</dependency>
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-web</artifactId>
	<version>1.2.3</version>
</dependency>
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-spring</artifactId>
	<version>1.2.3</version>
</dependency>
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-ehcache</artifactId>
	<version>1.2.3</version>
</dependency>
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-quartz</artifactId>
	<version>1.2.3</version>
</dependency>
```

也可以引入shiro-all引入shiro所有的jar包

```java
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-all</artifactId>
	<version>1.2.3</version>
</dependency>

```

# shiro 认证 #
## 认证流程 ##
<img src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20171107101602.png" style="width: 250px;height: 500px">

## 认证入门程序（用户登录和退出） ##

### 创建一个项目并导入shiro的jar包 ###
我这里创建的是一个maven项目，导入jar包直接加入shiro依赖就可以了

### shiro.ini ###
通过shiro.ini 配置文件初始化SecurityManager环境
为了方便测试将用户名密码配置在shiro.ini配置文件中：
```java
[users]
zhangsan=123
lisi=123
```
### 认证代码 ###
```java 
 // 用户登陆、用户退出
    @Test
    public void testLoginLogout() {

        // 构建SecurityManager工厂，IniSecurityManagerFactory可以从ini文件中初始化SecurityManager环境
        IniSecurityManagerFactory factory = new IniSecurityManagerFactory(
                "classpath:shiro.ini");

        // 通过工厂创建SecurityManager
        SecurityManager securityManager = factory.getInstance();

        // 将securityManager设置到运行环境中
        SecurityUtils.setSecurityManager(securityManager);

        // 创建一个Subject实例，该实例认证要使用上边创建的securityManager进行
        Subject subject = SecurityUtils.getSubject();

        // 创建token令牌，记录用户认证的身份和凭证即账号和密码
        UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");

        try {
            // 用户登陆
            subject.login(token);
        } catch (AuthenticationException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

        // 用户认证状态

        Boolean isAuthenticated = subject.isAuthenticated();

        System.out.println("用户认证状态：" + isAuthenticated);

        // 用户退出

        subject.logout();

        isAuthenticated = subject.isAuthenticated();

        System.out.println("用户认证状态：" + isAuthenticated);

    }

```

### 认证执行流程 ###
- 创建token令牌，token中有用户提交的认证信息即账号和密码
- 执行subject.login(token)，最终由securityManager通过Authenticator进行认证
- Authenticator的实现ModularRealmAuthenticator调用realm从ini配置文件取用户真实的账号和密码，这里使用的是IniRealm（shiro自带）
- IniRealm先根据token中的账号去ini中找该账号，如果找不到则给ModularRealmAuthenticator返回null，如果找到则匹配密码，匹配密码成功则认证通过。

### 常见的异常 ###
```java
UnknownAccountException
账号不存在异常如下：
org.apache.shiro.authc.UnknownAccountException: No account found for user。。。。

IncorrectCredentialsException
当输入密码错误会抛此异常，如下：
org.apache.shiro.authc.IncorrectCredentialsException: Submitted credentials for token [org.apache.shiro.authc.UsernamePasswordToken - zhangsan, rememberMe=false] did not match the expected credentials.
```
## 自定义Realm ##
上边的程序使用的是Shiro自带的IniRealm，IniRealm从ini配置文件中读取用户的信息，大部分情况下需要从系统的数据库中读取用户信息，所以需要自定义realm。

```java
public class CustomRealm1 extends AuthorizingRealm {
    @Override
    public String getName() {
        return "customRealm1";
    }

    //支持UsernamePasswordToken
    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof UsernamePasswordToken;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(
            AuthenticationToken token) throws AuthenticationException {

        //从token中 获取用户身份信息
        String username = (String) token.getPrincipal();
        //拿username从数据库中查询
        System.out.println(username);
        //....
        //如果查询不到则返回null
        if(!username.equals("zhang")){ //这里模拟查询不到
            return null;
        }

        //通过用户名获取从数据库查询出来的用户密码
        String password = "123";//这里使用静态数据模拟。。

        //返回认证信息由父类AuthenticatingRealm进行认证
        SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
                username, password, getName());

        return simpleAuthenticationInfo;
    }

    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(
            PrincipalCollection principals) {
        // TODO Auto-generated method stub
        return null;
    }

}
```

shiro-realm.ini
```java
[main]
#自定义 realm
customRealm=com.shiro.test.CustomRealm1
#将realm设置到securityManager
securityManager.realms=$customRealm
```
### 测试代码 ###
测试代码同入门程序，将ini的地址修改为shiro-realm.ini。
分别模拟账号不存在、密码错误、账号和密码正确进行测试。

## 散列算法 ##
散列算法一般用于生成一段文本的摘要信息，散列算法不可逆，将内容可以生成摘要，无法将摘要转成原始内容。散列算法常用于对密码进行散列，常用的散列算法有MD5、SHA。
一般散列算法需要提供一个salt（盐）与原始内容生成摘要信息，这样做的目的是为了安全性，比如：111111的md5值是：96e79218965eb72c92a549dd5a330112，拿着“96e79218965eb72c92a549dd5a330112”去md5破解网站很容易进行破解，如果要是对111111和salt（盐，一个随机数）进行散列，这样虽然密码都是111111加不同的盐会生成不同的散列值。

### 散列举例 ###
```java
    @Test
    public void test(){
        //md5加密，不加盐
        String password_md5 = new Md5Hash("111111").toString();
        System.out.println("md5加密，不加盐="+password_md5);

        //md5加密，加盐，一次散列
        String password_md5_sale_1 = new Md5Hash("111111", "abcdef", 1).toString();
        System.out.println("password_md5_sale_1="+password_md5_sale_1);
        String password_md5_sale_2 = new Md5Hash("111111", "abc", 1).toString();
        System.out.println("password_md5_sale_2="+password_md5_sale_2);
        //两次散列相当于md5(md5())

        //使用SimpleHash,结果等同于上边的Md5Hash
        System.out.println("散列一次："+new SimpleHash("MD5", "111111", "abcdef",1).toString());
        System.out.println("散列两次："+new SimpleHash("MD5", "111111", "abcdef",2).toString());

    }
```
### 在Realm中使用 ###
实际应用是将盐和散列后的值存在数据库中，自动realm从数据库取出盐和加密后的值由shiro完成密码校验。
```java
	//认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(
            AuthenticationToken token) throws AuthenticationException {

        //用户账号
        String username = (String) token.getPrincipal();
        //根据用户账号从数据库取出盐和加密后的值
        //..这里使用静态数据
        //如果根据账号没有找到用户信息则返回null，shiro抛出异常“账号不存在”

        //按照固定规则加密码结果 ，此密码 要在数据库存储，原始密码 是123，盐是abcdef
        String password = "aa7c9c12fc740955ef4dfad670250ff4";
        //盐，随机数，此随机数也在数据库存储
        String salt = "abcdef";

        //返回认证信息
        SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
                username, password, ByteSource.Util.bytes(salt),getName());

        return simpleAuthenticationInfo;
    }
```
配置shiro-realm2.ini
```java
[main]
#定义凭证匹配器
credentialsMatcher=org.apache.shiro.authc.credential.HashedCredentialsMatcher
#散列算法
credentialsMatcher.hashAlgorithmName=md5
#散列次数
credentialsMatcher.hashIterations=1

#将凭证匹配器设置到realm
customRealm=com.shiro.test.CustomRealm2
customRealm.credentialsMatcher=$credentialsMatcher
securityManager.realms=$customRealm

```
### 测试代码 ###
测试代码同上个章节，注意修改ini路径。

# shiro授权 # 
## 授权流程 ##
<img src="http://pursuedream-blog.oss-cn-beijing.aliyuncs.com/blogImg/20171108003601.png" style="width:300px;height:500px">

## 授权方式 ##
Shiro 支持三种方式的授权：
- 编程式：通过写if/else 授权代码块完成：
Subject subject = SecurityUtils.getSubject();
if(subject.hasRole(“admin”)) {
//有权限
} else {
//无权限
}
- 注解式：通过在执行的Java方法上放置相应的注解完成：
@RequiresRoles("admin")
public void hello() {
//有权限
}
- JSP/GSP 标签：在JSP/GSP 页面通过相应的标签完成：
<shiro:hasRole name="admin">
<!— 有权限—>
</shiro:hasRole>

### shiro-permission.ini ###
```java
[users]
#用户zhang的密码是123，此用户具有role1和role2两个角色
zhang=123,role1,role2
wang=123,role2

[roles]
#角色role1对资源user拥有create、update权限
role1=user:create,user:update
#角色role2对资源user拥有create、delete权限
role2=user:create,user:delete
#角色role3对资源user拥有create权限
role3=user:create
```
在ini文件中用户、角色、权限的配置规则是：“用户名=密码，角色1，角色2...” “角色=权限1，权限2...”，首先根据用户名找角色，再根据角色找权限，角色是权限集合。

### 权限字符串规则 ###
权限字符串的规则是：“资源标识符：操作：资源实例标识符”，意思是对哪个资源的哪个实例具有什么操作，“:”是资源/操作/实例的分割符，权限字符串也可以使用*通配符。

例子：
用户创建权限：user:create，或user:create:*
用户修改实例001的权限：user:update:001
用户实例001的所有权限：user：*：001

### 测试代码 ###

```java
	@Test
	public void testPermission() {

		// 从ini文件中创建SecurityManager工厂
		Factory<SecurityManager> factory = new IniSecurityManagerFactory(
				"classpath:shiro-permission.ini");

		// 创建SecurityManager
		SecurityManager securityManager = factory.getInstance();

		// 将securityManager设置到运行环境
		SecurityUtils.setSecurityManager(securityManager);

		// 创建主体对象
		Subject subject = SecurityUtils.getSubject();

		// 对主体对象进行认证
		// 用户登陆
		// 设置用户认证的身份(principals)和凭证(credentials)
		UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");
		try {
			subject.login(token);
		} catch (AuthenticationException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		// 用户认证状态
		Boolean isAuthenticated = subject.isAuthenticated();

		System.out.println("用户认证状态：" + isAuthenticated);

		// 用户授权检测 基于角色授权
		// 是否有某一个角色
		System.out.println("用户是否拥有一个角色：" + subject.hasRole("role1"));
		// 是否有多个角色
		System.out.println("用户是否拥有多个角色：" + subject.hasAllRoles(Arrays.asList("role1", "role2")));
		
//		subject.checkRole("role1");
//		subject.checkRoles(Arrays.asList("role1", "role2"));

		// 授权检测，失败则抛出异常
		// subject.checkRole("role22");

		// 基于资源授权
		System.out.println("是否拥有某一个权限：" + subject.isPermitted("user:delete"));
		System.out.println("是否拥有多个权限：" + subject.isPermittedAll("user:create:1",	"user:delete"));
		
		//检查权限
		subject.checkPermission("sys:user:delete");
		subject.checkPermissions("user:create:1","user:delete");
	}

```

## 自定义Realm ##
与上边认证自定义realm一样，大部分情况是要从数据库获取权限数据，这里直接实现基于资源的授权。
### Realm代码 ###
在认证章节写的自定义realm类中完善doGetAuthorizationInfo方法，此方法需要完成：根据用户身份信息从数据库查询权限字符串，由shiro进行授权。

```java
// 授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		// 获取身份信息
		String username = (String) principals.getPrimaryPrincipal();
		// 根据身份信息从数据库中查询权限数据
		//....这里使用静态数据模拟
		List<String> permissions = new ArrayList<String>();
		permissions.add("user:create");
		permissions.add("user.delete");
		
		//将权限信息封闭为AuthorizationInfo
		
		SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
		for(String permission:permissions){
			simpleAuthorizationInfo.addStringPermission(permission);
		}
		
		return simpleAuthorizationInfo;
	}
```

### 执行授权流程 ###
- 执行subject.isPermitted("user:create")
- securityManager通过ModularRealmAuthorizer进行授权
- ModularRealmAuthorizer调用realm获取权限信息
- ModularRealmAuthorizer再通过permissionResolver解析权限字符串，校验是否匹配
















