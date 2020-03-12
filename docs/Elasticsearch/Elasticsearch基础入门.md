

## 一、ElasticSearch的简介

>ElasticSearch：分布式的搜索引擎。当项目中需要在大量数据中进行搜索时可以考虑使用es。

### 1.简介

提供高效率的搜索分析，比如它是ELK的一个组成，ELK代表的是：

E：`ElasticSearch`搜索和分析的功能

F：`Logstash`搜集数据的功能，日志收集系统

K：`Kibana`数据可视化，可以用图表的方式来展示，是数据可视化平台

![](/img/1简介.png)

### 2.优点

+ 1）分布式的功能
+ 2）数据高可用、集群高可用
+ 3）API简单、高级
+ 4）支持PB级别数据的搜索、分析

## 二、ES的核心概念

Elasticsearch是面向文档的，意味着它可以存储整个对象或文档。可以对文档进行索引、搜索、排序、过滤。

Elasticsearch对比传统关系型数据库如下：

![](/img/2对比mysql.png)



### 1.索引 index

一个索引就是一个拥有几分相似特征的文档的集合。由一个名字来标识，当我们要对这个索引中的文档进行索引、搜索、更新、删除的时候，都要使用到这个名字。

> index就是一个索引库。类似于我们Mysql里面的一个数据库 

### 2.类型 type

在一个索引中，可以定义一种或多种类型。一个类型是索引的一个逻辑上的分类/分区。好比数据库里面的一张表。

### 3.文档 document

文档就是最终的数据，可以认为一个文档就是一条记录。
是ES里面最小的数据单元，就好比表里面的一条数据

### 4.字段 Field

相当于是数据表的字段，对文档数据根据不同属性进行的分类标识。一个document有一个或者多个field组成

### 5.接近实时 NRT（Near Realtime）

写入数据、查询数据近实时

### 6.集群 cluster

一个集群就是由一个或多个节点组织在一起，它们共同持有整个的数据，并一起提供索引和搜索功能。一个集群由
一个唯一的名字标识，这个名字默认就是“elasticsearch”。这个名字是重要的，因为一个节点只能通过指定某个集
群的名字，来加入这个集群。

### 7.节点 node

一个节点是集群中的另一个服务器。

### 8.分片 shards

一台服务器，无法存储大量的数据，ES把一个index里面的数据，分为多个shard，分布式的存储在各个服务器上面。

### 9.副本 replicas

在es集群中，我们一模一样的数据有多份，能正常提供查询和插入的分片叫做主分片，其余的叫复制分片

## 三、kibana与es的交互

```properties
# 查看集群的健康状况
GET _cat/health

# 查询es中所有index
GET _cat/indices
GET _all

# 增加一个index库
PUT /aura_index

# 删除一个aura_index的index库
DELETE /aura_index

# 1.插入一条商品数据
# 库名/表名/一条数据的id号
# 我们插入数据的时候，如果我们的语句中指明了index和type，如果ES里面不存在，默认帮我们自动创建
PUT /order/goods/4
{
  "name":"货品4",
  "price": 1900,
  "memo":"这是一个测试货品444"
}

# 2.查询商品数据
GET /order/goods/1

# 3.修改商品数据(post/put都可以)
# POST是局部更新数据，别的数据不动。PUT是全局更新
POST /order/goods/3
{
  "name":"测试货品3",
  "price": 500,
  "memo":"这是一个测试货品333"
}

# 4.删除商品数据
DELETE /order/goods/1

# 5.查看所有数据
GET /order/goods/_search

# DSL语言
# 6.查询名称包含“测试”的商品，按价格进行降序排序
GET /order/goods/_search
{
  "query": {
    "match": {
      "name":"货品"
    }
  },
  "sort":[
    {
      "price":{
        "order":"desc"
      }
    }
    
  ]
}

# 7.实现分页查询（from、size）
GET /order/goods/_search
{
  "query": {
    "match": {
      "name":"货品"
    }
  },
  "sort":[
    {
      "price":{
        "order":"desc"
      }
    }
    
  ],
  "from": 0,
  "size": 2
}

# 8.返回指定字段内容
GET /order/goods/_search
{
  "query": {
    "match": {
      "name":"测试货品1"
    }
  },
  "_source":["name","memo"]
}
```

## 四、Mysql、ES数据同步（logstash）

>  全量、增量同步

下载logstash后，在logstash-7.6.0\config目录下新建mysql.conf文件

```properties
input {
  jdbc {
    # mysql相关jdbc配置
    jdbc_connection_string => "jdbc:mysql://localhost:3306/es_blog?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC"
    jdbc_user => "root"
    jdbc_password => "123456"

    # jdbc连接mysql驱动的文件目录，可去官网下载:https://dev.mysql.com/downloads/connector/j/
    jdbc_driver_library => "D:\\environment\\logstash-7.6.0\\mysql-connector-java-8.0.15.jar"
    # the name of the driver class for mysql
    jdbc_driver_class => "com.mysql.cj.jdbc.Driver"
    jdbc_paging_enabled => true
    jdbc_page_size => "50000"

    jdbc_default_timezone =>"Asia/Shanghai"

    # mysql文件, 也可以直接写SQL语句在此处，如下：
    statement => "select * from blog where update_time >= :sql_last_value and update_time < now() order by update_time desc"
    # statement_filepath => "./config/jdbc.sql"

    # 这里类似crontab,可以定制定时操作，比如每分钟执行一次同步(分 时 天 月 年)
    schedule => "* * * * *"
    #type => "jdbc"

    # 是否记录上次执行结果, 如果为真,将会把上次执行到的 tracking_column 字段的值记录下来,保存到 last_run_metadata_path 指定的文件中
    #record_last_run => true

    # 是否需要记录某个column 的值,如果record_last_run为真,可以自定义我们需要 track 的 column 名称，此时该参数就要为 true. 否则默认 track 的是 timestamp 的值.
    # use_column_value => true

    # 如果 use_column_value 为真,需配置此参数. track 的数据库 column 名,该 column 必须是递增的. 一般是mysql主键
    # tracking_column => "update_time"
    
    # tracking_column_type => "timestamp"

    # last_run_metadata_path => "./logstash_capital_bill_last_id"

    # 是否清除 last_run_metadata_path 的记录,如果为真那么每次都相当于从头开始查询所有的数据库记录
    clean_run => true

    #是否将 字段(column) 名称转小写
    # lowercase_column_names => false
  }
}

output {
  elasticsearch {
    hosts => "127.0.0.1:9200"
    index => "blog"
    document_id => "%{id}"
  }

  # 这里输出调试，正式运行时可以注释掉
  stdout {
      codec => json_lines
  } 
}
```

在bin目录下进入命令行窗口，输入logstash -f ../config/mysql.conf

此时会发现数据已同步，并且一分钟同步一次

## 五、ES中文分词器

> 安装ik分词器，第三方插件

**测试分词效果**

```properties
POST _analyze
{
  "analyzer": "ik_max_word",
  "text": "我是中国人"
}
```

![](/img/3测试分词效果.png)













