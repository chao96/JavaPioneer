> 每天对自己说一句：早安！

* [一、自定义持久层框架](#一自定义持久层框架)
  * [1.分析jdbc操作问题](#1分析jdbc操作问题)
  * [2.问题解决思路](#2问题解决思路)
  * [3.自定义框架设计](#3自定义框架设计)
  * [4.自定义框架实现](#4自定义框架实现)
  * [5.自定义框架优化](#5自定义框架优化)
* [二、Mybatis相关概念](#二mybatis相关概念)
  * [1.对象/关系数据库映射（ORM）](#1对象关系数据库映射orm)
  * [2.Mybatis简介](#2mybatis简介)
  * [3.Mybatis历史](#3mybatis历史)
  * [4.Mybatis优势](#4mybatis优势)
* [三、Mybatis基本应用](#三mybatis基本应用)
* [四、Mybatis配置文件学习](#四mybatis配置文件学习)
  * [1.SqlMapConfig.xml](#1sqlmapconfigxml)
  * [2.mapper.xml](#2mapperxml)
* [五、Mybatis复杂映射开发](#五mybatis复杂映射开发)
  * [1.一对一查询(association)](#1一对一查询association)
  * [2.一对多查询(collection)](#2一对多查询collection)
  * [3.多对多查询](#3多对多查询)
* [六、Mybatis注解开发](#六mybatis注解开发)
  * [1.常用注解](#1常用注解)
  * [2.增删改查](#2增删改查)
  * [3.注解实现复杂映射开发](#3注解实现复杂映射开发)
  * [4.一对一查询](#4一对一查询)
  * [5.一对多查询](#5一对多查询)
* [七、Mybatis缓存](#七mybatis缓存)
  * [1.一级缓存](#1一级缓存)
  * [2.二级缓存](#2二级缓存)
* [八、Mybatis插件](#八mybatis插件)
  * [1.插件编写范围](#1插件编写范围)
  * [2.运行原理](#2运行原理)
  * [3.编写插件](#3编写插件)
* [九、Mybatis架构原理](#九mybatis架构原理)
  * [1.架构设计](#1架构设计)
  * [2.主要构件及相互关系](#2主要构件及相互关系)
* [十、Mybatis源码剖析](#十mybatis源码剖析)
  * [1.传统方式源码剖析](#1传统方式源码剖析)
* [十一、设计模式](#十一设计模式)
* [十三、汇总出现的问题](#十三汇总出现的问题)
  * [1.加载配置文件为null？](#1加载配置文件为null)
  * [2.连接不上数据库？](#2连接不上数据库)


# 一、自定义持久层框架

## 1.分析jdbc操作问题

```java
public static void main(String[] args) {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    ResultSet resultSet = null;

    try {
        // 加载数据库驱动
        Class.forName("com.mysql.jdbc.Driver");
        // 通过驱动管理类获取数据库连接
        connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8", "root", "123456");
        // 定义sql语句
        String sql = "select * from user where username = ?";
        // 获取预处理statement
        preparedStatement = connection.prepareStatement(sql);
        // 设置参数，序号从1开始
        preparedStatement.setString(1, "tom");
        // 向数据库发出sql执行查询，获取结果集
        resultSet = preparedStatement.executeQuery();
        // 遍历查询结果集
        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String username = resultSet.getString("username");
            // 封装user
            User user = new User();
            user.setId(id);
            user.setUsername(username);
            System.out.println(user);
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        // 释放资源
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (preparedStatement != null) {
            try {
                preparedStatement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**JDBC问题总结：**

1.数据库连接创建、释放频繁造成系统资源浪费，从而影响系统性能。

2.Sql语句在代码中硬编码，代码不易维护。

3.PrepareStatement向占位符传参硬编码

4.对结果集解析存在硬编码

## 2.问题解决思路

+ 数据库连接频繁创建、释放：连接池
+ sql语句及参数硬编码：配置文件
+ 解析封装返回结果集：反射、内省

## 3.自定义框架设计

**使用端：**

提供核心配置文件

sqlMapConfig.xml：存放数据源信息，引入mapper.xml

Napper.xml：sql语句的配置文件信息

**框架端：**

①读取配置文件

读取完以后以流的形式存在，我们不能将读取到的配置信息以流的形式存放在内存中，不好操作，可以创建javaBean来存储。

1）Configuration：存放数据库基本信息、Map<唯一标识, Mapper>

2）MappedStatement：sql语句、statement类型、输入参数Java类型、输出参数java类型等

②解析配置文件

创建SqlSessionFactoryBuilder、SqlSessionFactory类；

方法：SqlSessionFactory build();

第一：使用domj解析配置文件，将解析出来的内容封装到Configuration和MappedStatement中

第二：创建SqlSessionFactory的实现类DefaultSqlSessionFactory

③创建SqlSessionFactory：

方法：openSession（）：获取sqlSession接口的实现类实例对象

④创建SqlSession接口及实现类：主要封装CRUD方法

方法：selectList（String Statementld，Object param）：查询所有

selectOne（String Statementld，Object param）：查询单个

close（）释放资源

具体实现：封装JDBC完成对数据库表的查询操作

**设计到的设计模式**

构建者模式、工厂模式、代理模式

## 4.自定义框架实现

1）使用端创建配置文件sqlMapConfig.xml

```xml
<configuration>
        <!--数据库配置信息-->
    <dataSource>
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/_0325_mybatis?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC"></property>
        <property name="username" value="root"></property>
        <property name="password" value="123456"></property>
    </dataSource>

    <!--存放mapper.xml的全路径-->
    <mapper resource="UserMapper.xml"></mapper>
</configuration>
```

2）maven依赖

```xml
<dependencies>
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>8.0.19</version>
	</dependency>
	<dependency>
		<groupId>com.mchange</groupId>
		<artifactId>c3p0</artifactId>
		<version>0.9.5.2</version>
	</dependency>
	<dependency>
		<groupId>log4j</groupId>
		<artifactId>log4j</artifactId>
		<version>1.2.12</version>
	</dependency>
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.10</version>
	</dependency>
	<dependency>
		<groupId>dom4j</groupId>
		<artifactId>dom4j</artifactId>
		<version>1.6.1</version>
	</dependency>
	<dependency>
		<groupId>jaxen</groupId>
		<artifactId>jaxen</artifactId>
		<version>1.1.6</version>
	</dependency>
</dependencies>
```

3）其它文件（查看项目代码）

Configuration、MappedStatement、Resources、SqlSessionFactoryBuilder、XMLConfigerBuilder、XMLMapperBuilder、SqlSession、Executor等

## 5.自定义框架优化

上述自定义框架解决了频繁创建释放数据库连接、硬编码、手动封装结果集问题。但有以下问题：

+ dao的实现类重复编码（创建sqlsession、调用sqlsession方法、关闭sqlsession）
+ 参数statement的id硬编码

**解决：**使用代理模式创建接口的代理对象。

> 通过约定接口方法名与id相同

# 二、Mybatis相关概念

## 1.对象/关系数据库映射（ORM）

ORM框架的作用是将面向对象的操作转换成对数据库的操作。

## 2.Mybatis简介

MyBatis是一款优秀的基于ORM的半自动轻量级持久层框架，它支持定制化SQL、存储过程以及高级映射。

## 3.Mybatis历史

原是Apache下一个开源项目iBatis

## 4.Mybatis优势

sql和java编码分开，功能边界清晰。

# 三、Mybatis基本应用

可以在MyBatis官网学习一下：[MyBatis官网](http://www.mybatis.org/mybatis-3/)

# 四、Mybatis配置文件学习

## 1.SqlMapConfig.xml

environments、mapper、properties等标签大致了解就行，可以翻阅文档。

## 2.mapper.xml

动态sql语句用法，此部分已比较熟悉。

# 五、Mybatis复杂映射开发

## 1.一对一查询(association)

```xml
<resultMap id="orderMap" type="com.lagou.pojo.Order">
	<result property="id" column="id"></result>
	<result property="orderTime" column="orderTime"></result>
	<result property="total" column="total"></result>

	<association property="user" javaType="com.lagou.pojo.User">
		<result property="id" column="uid"></result>
		<result property="username" column="username"></result>
	</association>
</resultMap>
```

## 2.一对多查询(collection)

## 3.多对多查询

collection

# 六、Mybatis注解开发

## 1.常用注解

```
@nsert：实现新增
@Update：实现更新@Delete：实现删除@Select：实现查询
@Result：实现结果集封装
@Results：可以与@Result一起使用，封装多个结果集
@One：实现一对一结果集封装
@Many：实现一对多结果集封装
```

## 2.增删改查

写相应注解及sql

## 3.注解实现复杂映射开发

可以使用@Results注解，@Result注解，@One注解，@Many注解组合完成复杂关系的配置；

> @Results代替的是标签<resultMap>
>
> @Result代替<result>和<id>标签

## 4.一对一查询

@One

## 5.一对多查询

@Many

# 七、Mybatis缓存

## 1.一级缓存

使用hashmap存储数据；Sessioin级别的缓存；执行修改或session关闭时失效

## 2.二级缓存

使用hashmap存储数据；namespace级别缓存；执行修改时失效

> 一级缓存默认开启，二级缓存需要手动开启

# 八、Mybatis插件

## 1.插件编写范围

Mybatis仅可以编写ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件

## 2.运行原理

使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，只会拦截那些你指定需要拦截的方法。

## 3.编写插件

+ 实现Mybatis的Interceptor接口并复写intercept()方法
+ 给插件编写注解，指定要拦截哪一个接口的哪些方法
+ 配置文件中配置编写的插件

# 九、Mybatis架构原理

## 1.架构设计

我们把Mybatis的功能架构分为三层：

1）API接口层：提供给外部使用的接口API

2）数据处理层：SQL查找、SQL解析、SQL执行和执行结果映射处理。

3）基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理。这些共用的东西作为最基础的组件。

## 2.主要构件及相互关系

| 构建             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| SqlSession       | 顶层API，表示和数据库交互的会话，完成增删改查功能            |
| Executor         | Mybatis执行器，是Mybatis调度的核心，负责SQL语句的生成和查询缓存的维护 |
| StatementHandler | 封装了JDBC Statement操作，负责对JDBC Statement的操作，如设置参数、将结果集转换成list集合 |
| ParameterHandler | 将用户传递的参数转换成JDBC Statement所需参数                 |
| ResultSetHandler | 将ResultSet结果集对象转换成List集合                          |
| TypeHandler      | 负责java数据类型和jdbc数据类型之间的映射和转换               |
| MappedStatement  | 维护了一条select\|update\|delete\|insert节点的封装           |
| SqlSource        | 根据用户传递的parameterObject，动态生成SQL语句，将信息封装到BoundSql对象中 |
| BoundSql         | 表示动态生成的SQL语句以及相应的参数信息                      |



# 十、Mybatis源码剖析

## 1.传统方式源码剖析

**1）初始化**

MyBatis在初始化的时候，会将MyBatis的配置信息全部加载到内存中，使用org.apache.ibatis.session.Configuration实例来维护

Configuration对象结构和xml配置文件对象几乎相同

> 初始化配置文件信息本质就是创建Configuration对象，将解析的xml数据封装到Configuration中

mappedStatements是一个HashMap，存储时key=全限定类名+方法名，value=对应的MappedStatement对象。

**2）执行SQL流程**

**先介绍SqlSession**

```
SqlSession是一个接口，它有两个实现类：DefaultSqlSession（默认）和SqlsessionManager（弃用，不做介绍）

Sqlsession是MyBatis中用于和数据库交互的顶层类，通常将它与ThreadLoca1绑定，一个会话使用一个sqlsession，并且在使用完毕后需要close。
public class DefaultSqlSession implements SqlSession{
	private final Configuration configuration；
	private final Executor executor；
	
Sqlsession中的两个最重要的参数，configuration与初始化时的相同，Executor为执行器
```

**Executor的功能和作用**

+ 根据传递的参数，完成SQL语句的动态解析，生成Boundsql对象，供statementHandler使用；
+ 为查询创建缓存，以提高性能
+ 创建JDBC的Statement连接对象，传递给*StatementHandler*对象，返回List查询结果。

```
三种基本的Executor执行器：SimpleExecutor、ReuseExecutor、BatchExecutor。

SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象

ReuseExecutor：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后不关闭Statement对象，而是放置于Map内，供下一次使用。重复使用Statement对象。

BatchExecutor：执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。
```

# 十一、设计模式

Mybatis涉及的主要设计模式

| 模式         | Mybatis体现                    |
| ------------ | ------------------------------ |
| 建造者模式   | 如SqlSessionFactoryBuilder     |
| 工厂方法模式 | 如SqlSessionFactory等          |
| 代理模式     | Mybatis实现的核心如MapperProxy |

# 十三、汇总出现的问题

## 1.加载配置文件为null？

视频中Resources文件代码修改为：

```java
InputStream resourceAsStream = Resources.class.getClassLoader().getResourceAsStream(path);
```

## 2.连接不上数据库？

mysql8.0以上版本

1.jar包更新

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.19</version>
</dependency>

<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.2</version>
</dependency>
```

2.sqlMapConfig.xml文件修改

```xml
<property name="driverClass" value="com.mysql.cj.jdbc.Driver"></property>
<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/_0325_mybatis?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC"></property>
<property name="username" value="root"></property>
<property name="password" value="123456"></property>
```

3.视频中一处代码错误

XMLConfigBuilder文件中properties.getProperty获取的属性需要和sqlMapConfig.xml文件字段名保持一致

```java
comboPooledDataSource.setUser(properties.getProperty("username"));
```
