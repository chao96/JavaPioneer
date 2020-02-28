# Shiro

## 一、Shiro简介

1.Shiro是Java的一个安全框架

2.主要功能：身份认证、授权、加密、会话管理

3.**基本功能简介如下：**

![](F:\learn\docs\Shiro\img\1基本功能简介.png)

+ **Authentication：身份认证；**验证用户是否拥有相应身份。
+ **Authorization：权限验证；**验证某个已认证的用户是否拥有某个权限。
+ **Session Manager：会话管理；**用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中。
+ **Cryptography：加密；**保护数据安全，如密码加密存到数据库。
+ **Web Support：Web支持；**集成Web环境。
+ **Caching：缓存；**用户登录后，其角色、权限等信息不必每次去查，提高效率。
+ **Concurrency：Shiro支持多线程应用的并发验证；**如在一个线程中开启另一个线程，可以把权限自动传播过去
+ **Testing：测试支持；**
+ **Run As：允许一个用户假装为另一个用户身份进行访问**。
+ **Remember Me：记住我；**如一次登录后，下次不用再登录。

## 二、Shiro架构

**1.Shiro架构（外部来看）**

![image-20200226102130755](C:\Users\xuchao\AppData\Roaming\Typora\typora-user-images\image-20200226102130755.png)

+ **Subject：应用代码直接交互的对象；**Shiro对外API的核心是Subject，Subject代表了当前用户，与Subject的所有交互都会委托给SecurityManager；Subject是一个门面，SecurityManager是实际的执行者。
+ **SecurityManager：安全管理器；**所有与安全有关的操作都会与SecurityManager交互，它是Shiro的核心，负责与Shiro的其他组件进行交互。
+ **Realm：Shiro从Realm获取安全数据（如用户、角色、权限）。**SecurityManager要验证用户身份，需要从Realm获取相应的用户信息验证。

**2.Shiro架构（内部来看）**

![](F:\learn\docs\Shiro\img\3Shiro架构（内部）.png)

+ **Subject：任何可以与应用交互的用户**。
+ **SecurityManager：Shiro的核心；**所有具体的交互都通过SecurityManager控制，管理着所有Subject，负责进行认证、授权、会话及缓存的管理。相当于SpringMVC中的DispatcherServlet；
+ **Authenticator：负责Subject认证；**可以自定义实现，什么情况下算用户认证通过。
+ **Authorizer：控制用户权限；**
+ **Realm：可以认为是安全实体数据源；**由用户提供，一般在应用中都需要实现自己的Realm；
+ **SessionManager：管理Session生命周期的组件**。
+ **CacheManager：缓存控制器。**缓存用户信息。
+ **Cryptography：密码模块。**加密解密操作