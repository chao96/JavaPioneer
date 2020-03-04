## 一、库的管理
### 1.创建库
create database 【if not exists】 库名【 character set 字符集名】;

### 2.修改库
alter database 库名 character set 字符集名;

### 3.删除库
drop database 【if exists】 库名;


## 二、表的管理
### 1.创建表
create table 【if not exists】 表名(  
...字段名 字段类型 【约束】,  
...字段名 字段类型 【约束】,  
... ...  
...字段名 字段类型 【约束】   
)

### 2.修改表
#### ①.添加列
alter table 表名 add column 列名 类型 【first|after 字段名】;
#### ②修改列的类型或约束
alter table 表名 modify column 列名 新类型 【新约束】;
#### ③修改列名
alter table 表名 change column 旧列名 新列名 类型;
#### ④删除列
alter table 表名 drop column 列名;
#### ⑤修改表名
alter table 表名 rename 【to】 新表名;

### 3.删除表
drop table【if exists】 表名;

### 4.复制表
#### ①复制表的结构
create table 表名 like 旧表;
#### ②复制表的结构+数据
create table 表名   
select 查询列表 from 旧表【where 筛选】;
 
