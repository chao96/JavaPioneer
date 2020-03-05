**mysql一条更新语句的执行流程**

与查询流程不一样的是，更新流程涉及两个重要的日志模块：**redo log（重做日志）**和**bin log（归档日志）**

## 一、redo log（重做日志）

> mysql里如果每次更新操作都写磁盘，然后磁盘找到相应记录再更新，整个过程IO、查找成本较高。
>
> Mysql使用WAL技术，先写日志，再写磁盘。

当有记录更新时，innodb引擎先写记录到redo log里，并更新内存，这个时候更新操作就算完成了。同时Innodb会在适当时候，将操作记录更新到磁盘（系统空闲的时候）。

**有了redo log，InnoDB就可以保证即使数据库发生异常重启，之前提交的记录也不会丢失，这个能力称为crash-safe。**

## 二、bin log（归档日志）

redo log是Innodb引擎特有的，binlog是mysql server层的日志。

**区别**：

+ redo log是物理日志，记录的是“在某个数据页上做了什么修改”；binlog记录sql语句
+ redo log是循环写，空间固定；binlog是追加写，不会覆盖

## 三、两阶段提交

**更新语句大致流程：**

`update T set c=c+1 where id=2;`

+ 1.执行器找存储引擎取id=2这一行
+ 2.数据页是否在内存中，不在先从磁盘读入内存，再返回
+ 3.将c+1调用存储引擎更新到内存
+ 4.写入redo log，此时redo log处于prepare阶段
+ 5.写入binglog
+ 6.提交事务，redolog改为commit状态
+ 7.更新完成

**为什么需要两阶段提交**：

若不采用两阶段

1**.先写redo log后写binglog**。redolog写完，binlog未写时，mysql异常重启。系统恢复时会读redo log恢复数据，但binlog无记录，如果用binlog恢复临时库会数据不一致。

2.**先写binlog后写redolog**。同理binlog和redolog不一致。

**采用两阶段提交**：

在update过程中，mysql宕机时：

1.若redolog写入处于prepare状态，binlog未写入，那么宕机重启后，redolog中这个事务会回滚。

2.若redolog写入了，binlog也写入了，但redolog还没更新为commit。宕机重启后，mysql检查binlog是否完整，是则提交事务。

## 四、怎么让数据库恢复到半个月内任意一秒状态

1.找最近的一次全量备份，恢复到临时库。

2.从备份时间点开始，执行对应的binlog



**回顾问题**：

1.为什么需要redo log？

更新数据是以页为最小单位更新。如果没有redolog每次更新数据都要刷到磁盘，而且是随机io。

2.为什么binlog做不到crash-safe？