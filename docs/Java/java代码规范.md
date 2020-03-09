## 一、编程规约

### (一) 命名风格

1.变量、参数、方法名，统一使用`lowerCamelCase`风格，必须遵循驼峰形式，且能见名知意。

**正例**：`orderNo` / `checkOrderInfo`() / `getGoodsName`()



2.常量命名全部大写，单词间用下划线分隔，语义表达完整清楚。

**正例**：`DEFAULT_PAGE_SIZE`

**反例**：`defaultPageSize` / `DEFAULT_SIZE`



3.`POJO`类中布尔类型的变量，都不要加 `is` ，否则部分框架解析会引起序列化错误。

**反例**：定义布尔类型 `Boolean isSuccess;` 的属性，它的方法也是 `isSuccess()` ，`RPC`框架在反向解析的时候，认为对应的属性名称是 `success`，导致属性获取不到。



4.包名统一使用小写，点分隔符之间仅有一个英语单词。包名统一使用
单数形式。

**正例**：应用工具类包名为 `com.differ.jackyun.oms.common.util`



5.禁止随意命名和完全不规范的缩写。

**反例**：如定义变量`int a`；定义枚举值`A、B、C、D`



6.`Serivce/DAO`层方法命名规约

> 1） 获取单个对象的方法用 get 做前缀。
> 2） 获取多个对象的方法用 list 做前缀。
> 3） 统计总数的方法用 count 做前缀。
> 4） 插入的方法用 save/insert 做前缀。
> 5） 删除的方法用 remove/delete 做前缀。
> 6） 修改的方法用 update 做前缀。

### (二) 代码规范

1.单个方法的总行数不超过100行；类文件总行数不超过3000行。



2.方法体内，一段逻辑与一段逻辑之间保留一个空行，禁止不空行密集的写下去，也禁止随意空行。



3.`controller`层不应包含业务逻辑，业务逻辑交给`service`层。`controller`的功能只有以下五点：

1）参数校验

2）转换业务/数据对象

3）调用`service`层接口实现业务逻辑（仅调用一个service接口）

4）组装返回对象

5）异常处理（推荐controller层抛出异常，拦截器统一处理）



4.代码中不应出现`System.out.println()` 和 `e.printStackTrace()`，用相应级别的Log日志替代。

**正例**：

```java
logger.debug("print id: {} and value : {} ", id, value);
```

**反例**：

```java
// 代码中出现
System.out.println();
OR
e.printStackTrace();
```



5.方法入参不能大于5个，大于5个时建议定义DTO对象接收。

**正例**：

```java
// jsonStr为Dto对象的json串
public void function(String jsonStr){
    // ......
}
```

**反例**：

```java
// 参数大于5个
public void function(String param1,String param2...){
	// ......
}
```



6.不允许正则表达式直接出现在代码中，应放于常量类中维护。



7.集合初始化时，指定集合初始值大小

**说明**：`HashMap` 使用 `HashMap(int initialCapacity)` 初始化， 

**正例**：`initialCapacity` = (需要存储的元素个数 / 负载因子) + 1。注意负载因子（即 `loader`  

`factor`）默认为 0.75，如果暂时无法确定初始值大小，请设置为 16（即默认值）。 

**反例**：`HashMap` 需要放置 1024 个元素，由于没有设置容量初始大小，随着元素不断增加，容 

量 7 次被迫扩大，`resize` 需要重建 `hash` 表，严重影响性能。 



8.字符串的连接方式：循环体内，使用 `StringBuilder` 的 `append` 方法进行连接。 

说明：`String`直接拼接时每次会 `new` 出一个 `StringBuilder` 对象，然后进行 

`append` 操作，最后通过 `toString` 方法返回 `String` 对象，造成内存资源浪费。

**正例**：

```java
StringBuilder strBuilder = new StringBuilder("hello");
for (int i = 0; i < 100; i++) {
    strBuilder.append("world");
}
```

**反例**： 

```java
String str = "hello";
for (int i = 0; i < 100; i++) {
    str = str + "world";
}
```



9.循环体中，若无必要禁止循环查库和调用外部接口（如不支持批量，需联系相应小组调整）；插入、修改、删除操作尽可能调整批量；

**正例**：

```java
private List<Order> func(List<Long> ids){
	return orderService.listByIds(ids);
}
```

**反例**：

```java
private List<Order> func(List<Long> ids){
	List<Order> orderList = new ArrayList<>();
	for(Long id : ids){
		Order order = orderService.getOrderByid();
		if(order != null && order.isEmpty()){
			orderList.add(order)
		}
	}
	return orderList;
}
```



10.状态值、业务相关常量不要直接出现在代码中，必须定义成相应枚举类。

**反例**：

```java
// 货品类型为1时，执行相应逻辑
if(Objects.equals(goodsType, 1)){
	// ......
}
```



11.移除代码中无效参数、变量、代码段。

**说明**：无效参数、变量必须移除，临时注释的代码段可以保留。



12.`if、for、while`后若只有一条语句，也必须添加大括号{}，不可省略。

**正例**：

```java
if(condition){
    return obj;
}
```

**反例**：

```java
if(condition) return obj;
```



13.调用外部接口需要对异常情况进行处理。

**说明**：当发生异常时，是阻断当前业务抛出异常，还是捕获处理使当前业务流程继续进行。



14.`Service A`不应直接调用`B的Dao`层，而是应该调用B的Service层业务实现

**说明**：使代码结构清晰，可重用性提高；方便以后不同模块之间进行项目拆分。

**正例**：`tradeOrderGoodsService.getGoodsPrice()`内部封装有`TradeOrderGoodsDao`相关操作

```JAVA
@Service
public class TradeOrderServiceImpl implements ITradeOrderService {
	@Autowired
    private ITradeOrderGoodsService tradeOrderGoodsService;
    
    @Override
    public void matchPrice(){
        tradeOrderGoodsService.getGoodsPrice();
    }
}

@Service
public class TradeOrderGoodsService implements ITradeOrderGoodsService {
	@Autowired
    private TradeOrderGoodsDao tradeOrderGoodsDao;
    
    @Override
    public void getGoodsPrice(){
        tradeOrderGoodsDao.listGoods();
        // 其余操作，1.2.3
        ......
    }
}
```

**反例**：

```java
@Service
public class TradeOrderServiceImpl implements ITradeOrderService {
	@Autowired
    private TradeOrderGoodsDao tradeOrderGoodsDao;
    
    @Override
    public void matchPrice(){
        tradeOrderGoodsDao.listGoods();
        // 其余操作，1.2.3
        ......
    }
}
```



15.`switch` 一定要有`default`语句，另外每个`case`后要加`break`，使程序更安全。

**正例**：

```java
switch (typeEnum) {
    case TradeStatusEnum.AUDIT:
        // ......
        break;
    case TradeStatusEnum.REVIEW:
        // ......
        break;
    default:
        break;
}
```



16.枚举类字段应定义为`final`类型，防止生成`set`方法，



17,返回类型为集合的方法，当返回的结果为`null`时，建议返回空集合，防止外部无校验时报错。

**正例**：

```java
public List<TradeOrder> listTradeOrder(List<Long> tradeIds){
    if(tradeIds == null || tradeIds.isEmpty()){
    	return Collections.emptyList();
    }
	// ......
}
```

**反例**：

```java
public List<TradeOrder> listTradeOrder(List<Long> tradeIds){
    if(tradeIds == null || tradeIds.isEmpty()){
    	return null;
    }
	// ......
}
```



18.不要以`password`（不区分大小写）作为字段名、方法名。



19.`equals`方法和`hashcode`方法必须同时重写

**说明**：重写`equals`而不重写`hashcode`方法，那么`hashcode`方法就是`Object`默认的`hashcode`方法，由于默认的`hashcode`方法是根据对象的**内存地址经哈希算法**得来的，**显然此时两对象不一定相等**。



20.`java`基本数据类型有默认值，注意不要踩坑，如`boolean`类型默认值为`false`。



21.``BigDecimal`和`String`一样具有不可变性，在做运算时一定要保存结果。

**反例**：

```java
BigDecimal total = BigDecimal.valueOf(10);
BigDecimal count = BigDecimal.valueOf(5);
total.add(count);
System.out.println(total); // total的值仍为10
```

**正例**：

```java
BigDecimal total = BigDecimal.valueOf(10);
BigDecimal count = BigDecimal.valueOf(5);
total = total.add(count);
System.out.println(total); // total的值为15
```



22.使用工具类 `Arrays.asList()`把数组转换成集合时，不能使用其修改集合相关的方法，它的 `add/remove/clear` 方法会抛出 `UnsupportedOperationException` 异常。



23.接口入参应转换成相应对象，不能直接转换为`JSONObject`进行取值

**正例**：

```java
public void list(String jsonStr){
    FilterOrderDto filterOrderDto = JSON.parseObject(jsonStr, FilterOrderDto.class);
    orderService.listOrderByCondition(filterOrderDto);
}
```

**反例**：后面维护时，不知道相应字段含义

```java
public void list(String jsonStr){
    JSONObject jsonObject = JSON.parseObject(jsonStr);
 	orderService.listOrderByCondition(jsonObject.getByte("isBatch"),
 		jsonObject.getString("requestList"));
}
```



24.如果需要用到`Map`中的`K-V`对，那就使用`entrySet` 遍历，而不是 `keySet`遍历。

**说明**：`keySet` 其实是遍历了 2 次，一次是转为 `Iterator` 对象，另一次是从 `hashMap` 中取出 

`key` 所对应的 `value`；`entrySet` 只是遍历了一次就把 `key` 和 `value` 都放到了 `entry` 中。



25.字符串转化使用`String.valueOf(value)` 代替 `" " + value`。



26.`string.split(String regex)`部分关键字需要转译，否则容易出错。

**说明**： 使用字符串`String 的plit` 方法时，传入的分隔字符串是正则表达式，部分关键字（比如 `.[]()|` 等）需要转义。

**反例**：

```java
String[] split = "a.ab.abc".split(".");
System.out.println(Arrays.toString(split)); // 结果为[]

String[] split1 = "a|ab|abc".split("|");
System.out.println(Arrays.toString(split1));// 结果为["a", "|", "a", "b", "|", "a", "b", "c"]
```

**正例**：

```java
// . 需要转译
String[] split2 = "a.ab.abc".split("\\.");
System.out.println(Arrays.toString(split2));  // 结果为["a", "ab", "abc"]

// | 需要转译
String[] split3 = "a|ab|abc".split("\\|");
System.out.println(Arrays.toString(split3));  // 结果为["a", "ab", "abc"]
```

### (三) 代码技巧性

1.`if-else`嵌套层数过多，当方法较复杂时应进行拆分，或改写成使用卫语句，不满足条件时直接退出。

**反例**：逻辑嵌套4层

```java
public void method(List<TradeOrder> tradeOrderList){
    if(tradeOrderList != null){
        for(TradeOrder tradeOrder: tradeOrderList){
            if(Objects.equals(tradeOrder.getTradeStatus(), 		                   TradeStatus.AUDIT_WAIT.getCode())){
                // ......
                if(...){
                    //......
                }
            }
        }
    }
}
```

**正例**：逻辑嵌套两层

```java
public void method(List<TradeOrder> tradeOrderList){
    if(tradeOrderList == null || tradeOrderList.isEmpty()){
        return;
    }
    for(TradeOrder tradeOrder: tradeOrderList){
        if(!Objects.equals(tradeOrder.getTradeStatus(), 		       TradeStatus.AUDIT_WAIT.getCode())){
            continue;
        }
        // ......
        if(...){
            //......
        }
    }
}
```



2.excel导出应该与列表查询调用相同方法，防止导出数据和查询数据不一致及维护多处代码。



3.查询列表`list`接口和总数`count`接口应分离，`count`接口应剔除不相干的关联表。



### (四) 事务相关

1.仅进行一次数据库的操作，无需添加事务



2.事务范围尽可能小，将查询数据等非数据库修改操作剥离。



3.事务不生效问题排查（事务传播属性为默认级别required）

**说明**：

```properties
1）数据库引擎是否为InnoDB，MyISAM不支持事务操作。
2）入口方法必须为public，否则事务不起作用。private、final、static方法不能添加事务，加了也不生效。
3）Spring事务管理默认只对运行期异常(java.lang.RuntimeException)进行回滚。
4）业务和事务入口在同一线程中，否则事务不生效。
5）（重要）①同一个Service内部，方法A直接调用加了@Transactiona注解的方法B，会发现注解根本没有生效。需要	用（MakelnvoiceServicelmpl）AopContext.currentProxy0）代理的方式来调用。不同的Service调用，注解有效。
	②@Transactional 注解触发回滚操作是通过异常，所以执行事务的方法，无需在代码内部针对每一句数据库操作判断是否有异常。但是涉及业务类的逻辑如果通不过，需要人工抛出异常来触发回滚（比如常见的库存不足）。
```

**补充**：代理方式调用方法- `((BService) AopContext.currentProxy()).B()`



### (五) 注释相关

1.类、类属性、类方法必须加`Javadoc`注释，即/** 内容 */格式。

**说明**：IDE中，鼠标移到方法上可悬浮提示方法、参数、返回值意义。



2.类名需要加上时间、作者、描述。



3.所有的枚举类型字段必须有相应注释，说明每个字段及枚举项的用途。



4.迭代业务逻辑变更时，相应的代码注释也需要同步修改，方便他人阅读。



### (六) 工具方法使用

1.`equals`判断建议用`Objects.equals(value1, value2)`；

**说明**：`Objects.equals`源码

```java
public static boolean equals(Object a, Object b) {
    return (a == b) || (a != null && a.equals(b));
}
```

**反例**：当`value1`为`null`时，会报`NPE`

```java
if(value1.equals(value2)){}
```



2.应使用`BigDecimal.valueOf()` 和 `new BigDecimal(string)`，禁止使用`new BigDecimal(double)`。

**说明**：

```java
public static void main(String[] args) {
    BigDecimal bigDecimal1 = new BigDecimal(0.99);
    BigDecimal bigDecimal2 = new BigDecimal("0.99");
    BigDecimal bigDecimal3 = BigDecimal.valueOf(0.99);

    System.out.println(bigDecimal1);
    System.out.println(bigDecimal2);
    System.out.println(bigDecimal3);
}
```

**结果**：

```java
0.9899999999999999911182158029987476766109466552734375
0.99
0.99
```



### (七) 其它

1.`Stream`之`list`转`map`的坑，及问题解决。

①`List`转`Map`，`Map`对应的`value`值为`null`时，会报`NPE`

> `Exception in thread "main" java.lang.NullPointerException`

**反例**：

```java
//声明一个List集合
List<Person> list = new ArrayList();
list.add(new Person("1001", "诸葛孔明"));
list.add(new Person("1002", "赵子龙"));
list.add(new Person("1003", null)); //map的value值为null，导致NPE
//将list转换map
Map<String, String> map = list.stream().collect(Collectors.toMap(Person::getId, Person::getName));
```

**正例**：

```java
// 过滤掉空元素
Map<String, String> map = list.stream().filter(p -> p.getName() != null).collect(Collectors.toMap(Person::getId, Person::getName));
```



②`List`转`Map`，`map`的`key`重复，会报以下异常

> `java.lang.IllegalStateException:Duplicate key`

**正例**：

```java
// 重复时用后面的value 覆盖前面的value
Map<String, String> map = list.stream().collect(Collectors.toMap(Person::getId, Person::getName, (oldValue, newValue) -> newValue));
```



③`List`转`Map`并进行分组，`map`对应对象的`key`为`null`，会报NPE

**正例**：

```java
// 过滤掉空key
Map<String, List<Person>> map2 = list.stream().filter(p -> p.getId() != null).collect(Collectors.groupingBy(Person::getId));
```



## 二、异常处理

1.`try-catch-finally`中，finally块必须对资源对象、流对象进行关闭。

**说明**：如果 `JDK7` 及以上，可以使用 `try-with-resources` 方式。

`jdk7`及以后关闭资源方式：

```java
public static void main(String[] args) throws Exception {
	try(
		FileInputStream inputStream = new FileInputStream(new File("test"));
	){
		System.out.println(inputStream.read());
	} catch (IOException e){
		throw new RuntimeException(e.getMessage(),e);
	}
}
```

传统（`jdk7`以前关闭资源方式）：

```java
// 
public static void main(String[] args) throws Exception {
    FileInputStream inputStream = null;

    try {
        inputStream = new FileInputStream(new File("test.txt"));
        System.out.println(inputStream.read());
    } catch (IOException e) {
        throw new RuntimeException(e.getMessage(),e);
    } finally {
        if (inputStream != null) {
            try{
                inputStream.close();
            } catch(IOException e){
                throw new RuntimeException(e.getMessage(),e);
            }
        }
    }
}
```



2.禁止对大段代码进行`try-catch`，区分出非稳定代码进行捕获处理。



3.不能在 `finally` 块中使用 `return`，`finally` 块中的 `return` 返回后方法结束执行，不 会再执行 `try` 块中的 `return` 语句。 



## 三、数据库

### (一) 建表规范

1.数据库表必备三字段：`id, gmt_create, gmt_modified`

**说明**：`gmt_create, gmt_modified`字段在一些场合可以帮助我们快速查找问题。



2.表达是与否的字段，必须使用`is_xxx`的方式命名，数据类型为`unsigned tinyint`(1表示是，0表示否)。

**正例**：如表示逻辑删除字段名`is_deleted`，1表示删除，0表示未删除。



3.主键索引名为`pk_字段名`；唯一索引名为`uk_字段名`；普通索引名为`idx_字段名`。

**说明**：`pk_` 即 `primary key`；`uk_` 即 `unique key`；`idx_` 即 `index` 的简称



4.如果表字段含义变更或对字段表示的状态追加时，需要及时更新表字段注释。



### (一) SQL语句

1.数据量大的表，严禁使用左模糊或者全模糊搜索，如果需要请走搜索引擎解决。



2.`SQL` 性能优化的目标：至少要达到 `range` 级别，必须使用`explain`查看执行计划。



3.`sql.xml` 参数使用：`#{}`，不要使用`${}`, 此种方式容易出现 `SQL` 注入

**说明**：`# 和$ 对比`

`user`表

| user_id | user_name |
| ------- | --------- |
| 1       | zhang     |

```mysql
一、使用#
<select id="selectUser" resultMap="BaseResultMap">
	select u.user_id,u.user_name from user u
	where u.user_name = #{userName}
</select>

1.userName="zhang"，返回{1,zhang}
2.userName="'zhang' or 1 = 1", 未查询到结果。

sql打印：
1.Preparing:select u.user_id,u.user_name from user uwhere u.user_name = ?
Parameters:"zhang"

2.Preparing:select u.user_id,u.user_name from user uwhere u.user_name = ?
Parameters:"'zhang' or 1 = 1"

二、使用"$"的注入问题
<select id="selectUser" resultMap="BaseResultMap">
	select u.user_id,u.user_name from user u
	where u.user_name = ${userName}
</select>

1.userName="zhang"，返回[{1,zhang}]
2.userName="'zhang' or 1 = 1", 返回[{1,zhang}]

sql打印：
1.select u.user_id,u.user_name from user u
	where u.user_name = 'zhang'

2.select u.user_id,u.user_name from user u
	where u.user_name = 'zhang' or 1 = 1

```

4.执行`sql`语句前，需对必要参数进行校验，避免出现所有参数为空时的`sql`报错情况。



5.防止因字段类型不同造成的隐式转换，从而导致索引失效。



6.`count(*)`会统计值为 `NULL` 的行，而 `count(列名)`不会统计此列为 `NULL` 值的行。



7.`in` 操作需评估 `in` 后边的集合元素数量，人工控制每次集合元素数量在1000个之内，分批进行查询。



8.在表查询中，一律不要使用 * 作为查询的字段列表，需要哪些字段必须明确写明。