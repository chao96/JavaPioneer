* [一、JDBC概述](#%E4%B8%80jdbc%E6%A6%82%E8%BF%B0)
* [二、数据库的连接](#%E4%BA%8C%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E8%BF%9E%E6%8E%A5)
* [三、Statement](#%E4%B8%89statement)
  * [1\.简单封装JDBCUtils](#1%E7%AE%80%E5%8D%95%E5%B0%81%E8%A3%85jdbcutils)
  * [2\.Statement实现CRUD操作（了解）](#2statement%E5%AE%9E%E7%8E%B0crud%E6%93%8D%E4%BD%9C%E4%BA%86%E8%A7%A3)
  * [3\.Statement使用的弊端](#3statement%E4%BD%BF%E7%94%A8%E7%9A%84%E5%BC%8A%E7%AB%AF)
* [四、PreparedStatement替换Statement](#%E5%9B%9Bpreparedstatement%E6%9B%BF%E6%8D%A2statement)
  * [1\.CRUD操作](#1crud%E6%93%8D%E4%BD%9C)
  * [2\.实现高效的批量插入](#2%E5%AE%9E%E7%8E%B0%E9%AB%98%E6%95%88%E7%9A%84%E6%89%B9%E9%87%8F%E6%8F%92%E5%85%A5)
* [五、数据库的事务](#%E4%BA%94%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E4%BA%8B%E5%8A%A1)
  * [1\.事务处理的原则：](#1%E4%BA%8B%E5%8A%A1%E5%A4%84%E7%90%86%E7%9A%84%E5%8E%9F%E5%88%99)
  * [2\.代码体现](#2%E4%BB%A3%E7%A0%81%E4%BD%93%E7%8E%B0)
  * [3\.事务的属性](#3%E4%BA%8B%E5%8A%A1%E7%9A%84%E5%B1%9E%E6%80%A7)
  * [4\.总结（数据库事务操作思想）](#4%E6%80%BB%E7%BB%93%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BA%8B%E5%8A%A1%E6%93%8D%E4%BD%9C%E6%80%9D%E6%83%B3)
* [六、数据库连接池](#%E5%85%AD%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5%E6%B1%A0)
  * [1\.传统连接的问题](#1%E4%BC%A0%E7%BB%9F%E8%BF%9E%E6%8E%A5%E7%9A%84%E9%97%AE%E9%A2%98)
  * [2\.数据库连接池的好处](#2%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5%E6%B1%A0%E7%9A%84%E5%A5%BD%E5%A4%84)
  * [3\.具体实现](#3%E5%85%B7%E4%BD%93%E5%AE%9E%E7%8E%B0)
    * [1）druid（推荐）](#1druid%E6%8E%A8%E8%8D%90)
    * [2）C3P0](#2c3p0)

##  一、JDBC概述

**1.JDBC的理解** 

JDBC是sun公司提供的一套用于数据库操作的接口。java程序员只需要面向这套接口编程；不同的数据库厂商，针对这套接口，提供不同实现（即为不同数据库的驱动）。

## 二、数据库的连接

**1.最终版**

```java
public void getConnection() throws Exception{
	//1.读取配置文件中的4个基本信息
	InputStream is = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
	Properties pros = new Properties();
	pros.load(is);

	String user = pros.getProperty("user");
	String password = pros.getProperty("password");
	String url = pros.getProperty("url");
	String driverClass = pros.getProperty("driver");

	//2.加载驱动
	Class.forName(driverClass);

	//3.获取连接
	Connection conn = DriverManager.getConnection(url, user, password);
	System.out.println(conn);
}
```

**配置文件jdbc.properties**

```properties
user=root
password=123456
url=jdbc:mysql://localhost:3306/test?serverTimezone=UTC
driver=com.mysql.jdbc.Driver
```

**2.方式一、二、三、四，作为过程了解**

```java
// 方式一：
@Test
public void testConnection1() throws SQLException {
	// 获取Driver实现类对象
	Driver driver = new com.mysql.jdbc.Driver();

	String url = "jdbc:mysql://localhost:3306/test";
	// 将用户名和密码封装在Properties中
	Properties info = new Properties();
	info.setProperty("user", "root");
	info.setProperty("password", "abc123");

	Connection conn = driver.connect(url, info);
	System.out.println(conn);
}

// 方式二：对方式一的迭代:在如下的程序中不出现第三方的api,使得程序具有更好的可移植性
@Test
public void testConnection2() throws Exception {
	// 1.获取Driver实现类对象：使用反射
	Class clazz = Class.forName("com.mysql.jdbc.Driver");
	Driver driver = (Driver) clazz.newInstance();

	// 2.提供要连接的数据库
	String url = "jdbc:mysql://localhost:3306/test";

	// 3.提供连接需要的用户名和密码
	Properties info = new Properties();
	info.setProperty("user", "root");
	info.setProperty("password", "abc123");

	// 4.获取连接
	Connection conn = driver.connect(url, info);
	System.out.println(conn);
}

// 方式三：使用DriverManager替换Driver
@Test
public void testConnection3() throws Exception {
	// 1.获取Driver实现类的对象
	Class clazz = Class.forName("com.mysql.jdbc.Driver");
	Driver driver = (Driver) clazz.newInstance();

	// 2.提供另外三个连接的基本信息：
	String url = "jdbc:mysql://localhost:3306/test";
	String user = "root";
	String password = "abc123";

	// 注册驱动
	DriverManager.registerDriver(driver);

	// 获取连接
	Connection conn = DriverManager.getConnection(url, user, password);
	System.out.println(conn);
}

// 方式四：可以只是加载驱动，不用显示的注册驱动过了。
@Test
public void testConnection4() throws Exception {
	// 1.提供三个连接的基本信息：
	String url = "jdbc:mysql://localhost:3306/test";
	String user = "root";
	String password = "abc123";

	// 2.加载Driver
	Class.forName("com.mysql.jdbc.Driver");
	//相较于方式三，可以省略如下的操作：
	/*
	Driver driver = (Driver) clazz.newInstance();
	// 注册驱动
	DriverManager.registerDriver(driver);
	*/
	//为什么可以省略上述操作呢？
	/*
	 * 在mysql的Driver实现类中，声明了如下的操作：
	 * static {
			try {
				java.sql.DriverManager.registerDriver(new Driver());
			} catch (SQLException E) {
				throw new RuntimeException("Can't register driver!");
			}
		}
	 */

	// 3.获取连接
	Connection conn = DriverManager.getConnection(url, user, password);
	System.out.println(conn);
}
```
## 三、Statement

### 1.简单封装JDBCUtils

```java
public class JDBCUtils {
	
	/**
	 * 获取数据库的连接
	 * @return
	 * @throws Exception
	 */
	public static Connection getConnection() throws Exception {
		// 1.读取配置文件中的4个基本信息
		InputStream is = ClassLoader.getSystemClassLoader().getResourceAsStream("jdbc.properties");

		Properties pros = new Properties();
		pros.load(is);

		String user = pros.getProperty("user");
		String password = pros.getProperty("password");
		String url = pros.getProperty("url");
		String driverClass = pros.getProperty("driver");

		// 2.加载驱动
		Class.forName(driverClass);

		// 3.获取连接
		Connection conn = DriverManager.getConnection(url, user, password);
		return conn;
	}
	/**
	 * 关闭连接和Statement的操作
	 * @param conn
	 * @param ps
	 */
	public static void closeResource(Connection conn,Statement ps){
		try {
			if(ps != null)
				ps.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		try {
			if(conn != null)
				conn.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
	/**
	 * 关闭资源操作
	 * @param conn
	 * @param ps
	 * @param rs
	 */
	public static void closeResource(Connection conn,Statement ps,ResultSet rs){
		try {
			if(ps != null)
				ps.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		try {
			if(conn != null)
				conn.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		try {
			if(rs != null)
				rs.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
}
```
### 2.Statement实现CRUD操作（了解）

**1) 增、删、改**

```java
/*
 * 实现对数据表中数据的增删改操作
 */
public void update(String sql) {
  Connection conn = null;
  Statement st = null;
  try {
    // 1.获取数据库的连接
    conn = JDBCUtils.getConnection();

    // 2.创建一个Statement的实例
    st = conn.createStatement();

    // 3.根据提供的sql语句，执行
    st.execute(sql);

  } catch (Exception e) {
    e.printStackTrace();
  } finally {
    // 4.资源的关闭
    JDBCUtils.closeResource(conn, st);
  }
}
```

**2) 查询**

```java
/*
 * 实现对数据表的查询操作。需要使用结果集：ResultSet
 */
public <T> T get(String sql, Class<T> clazz) {
  T t = null;
  Connection conn = null;
  Statement st = null;
  ResultSet rs = null;
  try {
    conn = JDBCUtils.getConnection();
    st = conn.createStatement();
    rs = st.executeQuery(sql);

    // 获取结果集的元数据
    ResultSetMetaData rsmd = rs.getMetaData();
    // 获取结果集的列数
    int columnCount = rsmd.getColumnCount();

    if (rs.next()) {

      t = clazz.newInstance();

      for (int i = 0; i < columnCount; i++) {
        // //1. 获取列的名称
        // String columnName = rsmd.getColumnName(i+1);

        // 1. 获取列的别名
        // 注意：获取结果集中（相当于数据表）列的名称（别名）
        String columnName = rsmd.getColumnLabel(i + 1);

        // 2. 根据列名获取对应数据表中的数据
        Object columnVal = rs.getObject(columnName);

        // 3. 将数据表中得到的数据，封装进对象
        // 注意：反射根据Java中类的属性获取Field对象
        Field field = clazz.getDeclaredField(columnName);
        field.setAccessible(true);
        field.set(t, columnVal);
      }
      return t;
    }
  } catch (Exception e) {
    e.printStackTrace();
  } finally {
    // 4.资源的关闭
    JDBCUtils.closeResource(conn, st, rs);
  }
  return null;
}
```

### 3.Statement使用的弊端

1）存在拼串操作，繁琐

```java
// sql拼串
String sql = "insert into customers(name,email,birth)values('" + name +"','"+email+"','"+birth+"')";
```

2）SQL注入

```java
SELECT user,password FROM user_table WHERE user = '1' or ' AND password = '=1 or '1' = '1'
```

3）其他问题

Statement实现批量插入时，效率较低

## 四、PreparedStatement替换Statement

### 1.CRUD操作

**增删改操作**

```java
public void update(String sql,Object ...args){//sql中占位符的个数与可变形参的长度相同！
  Connection conn = null;
  PreparedStatement ps = null;
  try {
    //1.获取数据库的连接
    conn = JDBCUtils.getConnection();
    //2.预编译sql语句，返回PreparedStatement的实例
    ps = conn.prepareStatement(sql);
    //3.填充占位符
    for(int i = 0;i < args.length;i++){
      ps.setObject(i + 1, args[i]);//小心参数声明错误！！
    }
    //4.执行
    ps.execute();
  } catch (Exception e) {
    e.printStackTrace();
  }finally{
    //5.资源的关闭
    JDBCUtils.closeResource(conn, ps);
  }
}
```

**查询**

```java
public <T> List<T> getForList(Class<T> clazz,String sql, Object... args){
	Connection conn = null;
	PreparedStatement ps = null;
	ResultSet rs = null;
	try {
		conn = JDBCUtils.getConnection();
		ps = conn.prepareStatement(sql);
		for (int i = 0; i < args.length; i++) {
			ps.setObject(i + 1, args[i]);
		}

		rs = ps.executeQuery();
		// 获取结果集的元数据 :ResultSetMetaData
		ResultSetMetaData rsmd = rs.getMetaData();
		// 通过ResultSetMetaData获取结果集中的列数
		int columnCount = rsmd.getColumnCount();
		//创建集合对象
		List<T> list = new ArrayList<T>();
		while (rs.next()) {
			T t = clazz.newInstance();
			// 处理结果集一行数据中的每一个列:给t对象指定的属性赋值
			for (int i = 0; i < columnCount; i++) {
				// 获取列值
				Object columValue = rs.getObject(i + 1);

				// 获取每个列的列名
				// String columnName = rsmd.getColumnName(i + 1);
		//说明：如果sql中没给字段取别名，getColumnLabel()获取的就是列名
				String columnLabel = rsmd.getColumnLabel(i + 1);

				// 给t对象指定的columnName属性，赋值为columValue：通过反射
				Field field = clazz.getDeclaredField(columnLabel);
				field.setAccessible(true);
				field.set(t, columValue);
			}
			list.add(t);
		}
		return list;
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		JDBCUtils.closeResource(conn, ps, rs);
	}
	return null;
}
```

### 2.实现高效的批量插入

**原理**

PrepareStatement预编译sql语句后，当下次调用时不需要再次编译。Statement每执行一次都要对传入的语句编译一次。

**举例**

①Statement实现

```java
Connection conn = JDBCUtils.getConnection();
Statement st = conn.createStatement();
for(int i = 1;i <= 20000;i++){
	String sql = "insert into goods(name)values('name_" + i + "')";
	st.execute(sql);
}
```

②PreparedStatement

+ 方法：addBatch()、executeBatch()、clearBatch()
+ mysql服务器默认是关闭批处理的，我们需要通过一个参数，让mysql开启批处理的支持。?rewriteBatchedStatements=true 写在配置文件的url后面

```java
public void testInsert() {
	Connection conn = null;
	PreparedStatement ps = null;
	try {
		conn = JDBCUtils.getConnection();

		//设置不允许自动提交数据
		conn.setAutoCommit(false);

		String sql = "insert into goods(name)values(?)";
		ps = conn.prepareStatement(sql);
		for(int i = 1;i <= 1000000;i++){
			ps.setObject(1, "name_" + i);
			//1."攒"sql
			ps.addBatch();

			if(i % 500 == 0){
				//2.执行batch
				ps.executeBatch();

				//3.清空batch
				ps.clearBatch();
			}
		}
		//提交数据
		conn.commit();
	} catch (Exception e) {								
		e.printStackTrace();
	}finally{
		JDBCUtils.closeResource(conn, ps);
	}
}
```

## 五、数据库的事务

### 1.事务处理的原则：

一个事务中执行多个操作时，要么所有事物都被提交（commit），要么全部失败，整个事务回滚（rollback）到最初状态

**说明**

数据一旦提交，就不可回滚

+ DDL操作一旦执行，都会自动提交
+ DML默认情况下，一旦执行就会自动提交（可通过set autocommit = false的方式取消DML的自动提交）
+ 默认在关闭连接时，会自动的提交数据

### 2.代码体现

```java
public void testUpdateWithTx() {
  Connection conn = null;
  try {
    conn = JDBCUtils.getConnection();
    System.out.println(conn.getAutoCommit());//true
    //1.取消数据的自动提交
    conn.setAutoCommit(false);

    String sql1 = "update user_table set balance = balance - 100 where user = ?";
    update(conn,sql1, "AA");

    //模拟网络异常
    System.out.println(10 / 0);

    String sql2 = "update user_table set balance = balance + 100 where user = ?";
    update(conn,sql2, "BB");
    System.out.println("转账成功");

    //2.提交数据
    conn.commit();

  } catch (Exception e) {
    e.printStackTrace();
    //3.回滚数据
    try {
      conn.rollback();
    } catch (SQLException e1) {
      e1.printStackTrace();
    }
  }finally{
    //修改其为自动提交数据
    //主要针对于使用数据库连接池的使用
    try {
      conn.setAutoCommit(true);
    } catch (SQLException e) {
      e.printStackTrace();
    }
    JDBCUtils.closeResource(conn, null);
  }
}
```

### 3.事务的属性

**1）四大属性**

①原子性：事务中的操作要么都发生，要么都不发生

②一致性：事务必须使数据库从一个一致性状态变换到另一个一致性状态

③隔离性：并发执行的各个事务之间不能互相干扰

④持久性：事务一旦被提交，对数据库中数据的改变就是永久性的

**2）数据操作过程中可能出现的问题（针对隔离性）**

对于同时运行的多个事务，当这些事务访问数据库中相同数据时，若没有采取必要的隔离机制，会导致各种并发问题：

①脏读：T1读取了已经被T2更新但还没提交的字段。之后若T2回滚，T1读取的内容就是临时且无效的

②不可重复读：T1读取了一个字段，然后T2更新了该字段。之后T1再次读取此字段，读到的值不同

③幻读：T1从表中读取了一个字段，T2在表中插入了一些新的行。之后T1再次读取会多出几行。

**数据库的四种隔离级别**

①read uncommitted（读未提交）

>  可以读取未提交的数据。脏读、不可重复读、幻读都会出现

②read committed（读已提交）

> 读取提交的数据。但是可能多次读取的数据结果不一致（不可重复读，幻读）

③repeatable read(可重复读，MySQL默认隔离级别)

> 可以重复读取，但又幻读。即：读取的数据行不可写，但可以往表中新增数据。在mysql中其他事务新增的数据看不到，不会产生幻读。采用多版本并发控制（MVCC）机制解决幻读问题

④serializable

> 可读，不可写。像java中的锁，写数据必须等待另一个事务结束。

### 4.总结（数据库事务操作思想）

```
1.获取数据库的连接(手动、数据库连接池)
Connection conn = JDBCUtils.getConnection();
// 不自动提交
conn.setAutoCommit(false);                   

2.多个DML操作，作为一个事务出现
// 全部执行完后提交
conn.commit();

3.如果出现异常，则回滚：
conn.rollback();

4.关闭资源
JDBCUtils.closeResource(..,...,...);
```

## 六、数据库连接池

### 1.传统连接的问题

①连接资源不能重复利用。频繁的进行数据库连接操作会占用很多系统资源。

②每一次数据库连接使用完后都得断开。否则因程序异常而未能关闭，将导致数据库系统内存泄漏，最终将导致重启数据库

③不能控制被创建的连接数。系统资源会被毫无顾忌的分配出去，连接过多也可能导致内存泄漏，服务器崩溃。

### 2.数据库连接池的好处

+ 提高程序响应速度（减少创建连接相应的事件）
+ 降低资源消耗（重复使用已经提供好的连接）
+ 便于连接的管理（设置最大连接数，超时回收被占用连接）

### 3.具体实现

#### 1）druid（推荐）

①导入jar包

![img](F:\github_blogs\JavaPioneer\docs\JDBC\img\1.png)

②连接代码

```java
public class JDBCUtils {
	/**
	 * 使用Druid数据库连接池技术
	 */
	private static DataSource source1;
	static{
		try {
			Properties pros = new Properties();
			
			InputStream is = ClassLoader.getSystemClassLoader().getResourceAsStream("druid.properties");
			
			pros.load(is);
			
			source1 = DruidDataSourceFactory.createDataSource(pros);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	public static Connection getConnection3() throws SQLException{
		
		Connection conn = source1.getConnection();
		return conn;
	}
}
```

配置文件定义在src下：druid.properties

```properties
url=jdbc:mysql:///test
username=root
password=abc123
driverClassName=com.mysql.jdbc.Driver

initialSize=10
maxActive=10
```

#### 2）C3P0

**①导入jar包**

![](F:\github_blogs\JavaPioneer\docs\JDBC\img\2.png)

②连接代码

```java
public class JDBCUtils {
	/**
	 * 使用C3P0的数据库连接池技术
	 */
	private static ComboPooledDataSource cpds = new ComboPooledDataSource("hellc3p0");
	public static Connection getConnection1() throws SQLException{
		Connection conn = cpds.getConnection();
		return conn;
	}
}
```

其中，配置文件定义在src下。名为：c3p0-config.xml

```properties
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>

	<named-config name="hellc3p0">
		<!-- 提供获取连接的4个基本信息 -->
		<property name="driverClass">com.mysql.jdbc.Driver</property>
		<property name="jdbcUrl">jdbc:mysql://localhost:3306/test</property>
		<property name="user">root</property>
		<property name="password">abc123</property>
		
		<!-- 进行数据库连接池管理的基本信息 -->
		<!-- 当数据库连接池中的连接数不够时，c3p0一次性向数据库服务器申请的连接数 -->
		<property name="acquireIncrement">5</property>
		<!-- c3p0数据库连接池中初始化时的连接数 -->
		<property name="initialPoolSize">10</property>
		<!-- c3p0数据库连接池维护的最少连接数 -->
		<property name="minPoolSize">10</property>
		<!-- c3p0数据库连接池维护的最多的连接数 -->
		<property name="maxPoolSize">100</property>
		<!-- c3p0数据库连接池最多维护的Statement的个数 -->
		<property name="maxStatements">50</property>
		<!-- 每个连接中可以最多使用的Statement的个数 -->
		<property name="maxStatementsPerConnection">2</property>

	</named-config>
</c3p0-config>
```

