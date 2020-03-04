## 一、查询
```mysql
select 查询列表
from 表1 别名
连接类型 join 表2
on 连接条件
where 筛选
group by 分组列表
having 筛选
order by排序列表
limit 起始条目索引，条目数;
```

## 二、插入
### 1.方式一
insert into 表名(字段名,...) values(值,...);

**支持子查询**：
insert into 表名
查询语句

### 2.方式二
insert into 表名 set 字段=值,字段=值,...;

## 三、修改
### 1.修改单表记录
update 表名 set 字段=值,字段=值 【where 筛选条件】;

### 2.修改多表的记录
update 表1 别名   
left|right|inner join 表2 别名   
on 连接条件  
set 字段=值,字段=值   
【where 筛选条件】;

## 四、删除

### 1.删除单表记录
delete from 表名 【where 筛选条件】【limit 条目数】

### 2.级联删除
delete 别名1,别名2   
from 表1 别名   
inner | left | right join 表2 别名   
on 连接条件  
【where 筛选条件】

### 3.使用truncate
truncate table 表名

### 4.truncate与delete的区别
1.truncate删除后，如果再插入，标识列从1开始  
  delete删除后，如果再插入，标识列从断点开始
  
2.delete可以添加筛选条件  
 truncate不可以添加筛选条件
 
3.truncate效率较高

4.truncate没有返回值  
delete可以返回受影响的行数
    
5.truncate不可以回滚  
  delete可以回滚