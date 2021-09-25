## 创建表

**数据类型**

```properties
varchar(255): 可变长字符串，根据实际的数据长度动态分配空间
char(255): 定长字符串，定长分配空间
int(11): 整型 int
bigint: 长整形 long
float: 单精度
double: 双精度
date: 日期
datetime: 日期时间
clob: 字符大对象 --> 4G Character Large OBject
blob: 二进制大对象-->图片等
```

## 删除表

```sh
drop table if exist table_name;
```



## 多表链接

### 内连接

**等值链接：等量关系**

```sh
# 92：表的连接和筛选条件杂糅
mysql> select tele, book.*  from author, book where book.aid = author.id;
# 99：表链接条件独立， 筛选条件继续添加where即可， inner可以省略
mysql> select tele, book.*  from author inner join book on book.aid = author.id where id = 1 and ...
```

**非等值连接:非等量关系**

```sh
select 
	e.name, s.grade
from 
	emp e
inner join
	sal s
on 
e.sal between s.low and s.high
```

**自连接**

```sh
# 一张表emp 看成两张表a/b
select 
	a.name, b.name 
from 
	emp a
inner join 
	emp b
on a.leader_id = b.id
```

## 外链接

**左外链接**：以左表为主表， 连接右表， 左表字段始终显示

**右外链接**： 与左外链接相反

## 子查询

嵌套select语句

```sh
select
	...(select) # 只能返回一条记录
from
	...(select) # 可以讲子查询的结果当成一张临时表 起别名
where
 	...(select)
```

**找出每个岗位的平均工资及其工资的等级**

```sh
# 找出每个岗位的平均工资（临时表t）
select job, avg(sal) from emp group by job  
# 工资等级表 salgrade
select * from salgrade
# 
select
	t.*, s.level
from 
	(select job, avg(sal) as avgsal from emp group by job) as t
join 
	salgrade s
on 
	t.avgsal between s.low and s.high
```

**找出每个员工的部门名称，显示员工名，部门名**

```sh
select 
	e.name, 
	(select d.name from dept d where d.deptno = e.deptno) # 1⃣️
	as dname
from 
	emp e
	
#	1⃣️ 期望只能查出来1条记录
```

## 合并

```sh
select * from a
union
select * from b

# a有10条记录， b有38条记录， 则匹配次数为10 + 38 = 48次
# 如果用连接的话， 则匹配次数为 10*38 = 380次
```

## 分页

**limit**

```sh
limit start, length
#	按照薪资降序，取出前五个
select ename, sal
from emp
order by emp.sal desc
limit 0,5
# 参数： 页数pageNo， 记录数pageSize
#（pageNo-1）* pageSize-->start
```



## 范式

```properties
1NF: 列不可再分
2NF: 消除部份依赖
3NF:消除传递依赖
```

## 事务

**事务的四大特性**

```TEX
原子性：事务不可再分割
持久性:   事务提交后数据应该提交到硬盘存储
一致性:   数据操作整个过程守恒
隔离性： 事务之间相互独立
```

**四种隔离级别**

```properties
READ UNCOMMITTED: 读未提交
脏读 : 一个事务读到了另一个事务未提交的数据
READ COMMITTED: 读已提交
不可重复读 : 在一个事务内， 两次读到的数据不一致
REPEATED READ: 可重复读
幻读 :　幻读是事务非独立执行时发生的一种现象。例如事务T1对一个表中所有的行的某个数据项做了从“1”修改为“2”的操作，这时事务T2又对这个表中插入了一行数据项，而这个数据项的数值还是为“1”并且提交给数据库。而操作事务T1的用户如果再查看刚刚修改的数据，会发现还有一行没有修改，其实这行是从事务T2中添加的，就好像产生幻觉一样，这就是发生了幻读			
SERIALIZABLE:  串行化锁表
```

