# 试一下手写`having`关键字

> [个人博客仓库](/技术探索&评测/试一下手写`having`关键字.md)
---

## 1、题目
（1）找出平均工资大于等于 8000 的工作，写出SQL语句  
（2）已知：表格emp_table，字段job、salary  
（3）刚学数据查询的聚合函数、`group by`分组，还没学`having`



## 2、推导如下↓

**①**最容易想到的
```sql
mysql> select * from emp_table where avg( salary ) >= 8000;

ERROR 1111 (HY000): Invalid use of group function
```
Q：这是因为`where`条件不能使用聚合函数嘛🤨  
A：**聚合函数需要select查询得到**，直接放where里面是算不出来函数结果的


**②**于是我把聚合函数用别名替代，把这个别名放进`where`，期望得到一个平均工资>=8000的新表，再获得这个表的job
```sql
mysql> select job, avg( salary ) as avg_salary from emp_table where avg_salary >= 8000 ;

ERROR 1054 (42S22): Unknown column 'avg_salary' in 'where clause
```
  
Q：但是不知道为什么报错🤔  
A：执行顺序是**先`where`条件筛选** → **再select查询** → 聚合函数算出结果，这里的`avg_salary`还没算出来就执行`where`了


**③**我感觉可能是**②**的表没有说清楚，MySQL不知道要从哪个表来找job，于是我把各个过程产生的表用括号括起来（除了最后一步产生的表），同时依然用别名替代`avg( salary )`
```sql
mysql> 
select * 
from ( 
	select avg( salary ) as avg_salary 
	from emp_table 
) 
where avg_salary >= 8000 ;

# 每个派生表（子查询）必须有一个别名
ERROR 1248 (42000): Every derived table must have its own alias
```
不过报错**每个表都需要别名**


**④**于是我把中间表取名`table2`，终于没有报错了
```sql
mysql> 
select * 
from ( 
	select avg( salary ) as avg_salary 
	from emp_table 
) as table2 
where avg_salary >= 8000 ;

Empty set (0.00 sec)
```


**⑤**在**④**中获取结果为空，于是我调整了`avg_salary`的筛选范围
```sql
mysql> 
select * 
from ( 
	select avg( salary ) as avg_salary 
	from emp_table 
) as table2 
where avg_salary >= 5000 ;

+------------+
| avg_salary |
+------------+
|  7548.2759 |
+------------+
1 row in set (0.00 sec)
```
果然获得了结果，但是好像跟预期的不一样——`select *`应该展示所有数据才对，这里只展示了`avg_salary`


**⑥**
```sql
mysql> 
select id, job 
from ( 
	select avg( salary ) as avg_salary 
	from emp_table 
) as table2 
where avg_salary >= 5000 ;

ERROR 1054 (42S22): Unknown column 'id' in 'field list'
```
这里再联系**④****⑤**，我突然想到这里其实只会展示`avg_salary`，我猜是因为括号里面`select avg( salary )`返回的表格就是关于`avg( salary )`的表格——因为查询的就是它，或者**每次查询产生的表，只包括`select`的字段**（一句正确的废话）  

而且这个`avg_salary >= 5000`突然让我想到`select avg( salary ) as avg_salary from emp_table where avg_salary >= 5000 ;`其实表示当平均工资超过多少，才展示平均工资，我的语义搞混了😂


**⑦**我在想怎么把**⑥**的`select avg( salary ) as avg_salary from emp_table`拆开，这样就不会返回只有`avg( salary )`的表了，于是我又做了新的尝试
```sql
mysql> select job, avg( salary ) from emp_table;

ERROR 1140 (42000): In aggregated query without GROUP BY, expression #1 of SELECT list contains nonaggregated column 'hello_mysql.emp_table.job'; this is incompatible with sql_mode=only_full_group_by
```
这应该是因为涉及聚合函数查询时，必须满足`select 分组字段1, 聚合函数( 字段2 ) from 表名 group by 分组字段1`，也就是**查询的字段要么是分组字段、要么放在聚合函数里面**


**⑧**现在按job分组，得到了关于job、`avg_salary`（不同job对应的平均工资）的表
```sql
mysql> select job, avg( salary ) from emp_table group by job;

+------+---------------+
| job  | avg( salary ) |
+------+---------------+
|    4 |    15000.0000 |
|    2 |     9875.0000 |
|    3 |     6500.0000 |
|    1 |     5083.3333 |
|    5 |     5377.7778 |
| NULL |          NULL |
+------+---------------+
6 rows in set (0.00 sec)
```
于是我又想，为什么不可以**从这个表中筛选出想要的**job


**⑨**于是我对**⑧**的表，取出job
```sql
# 注意给产生的表取别名
mysql> 
select job 
from ( 
	select job, avg( salary ) 
	from emp_table 
	group by job 
) as table2;

+------+
| job  |
+------+
|    4 |
|    2 |
|    3 |
|    1 |
|    5 |
| NULL |
+------+
6 rows in set (0.00 sec)
```


**⑩**那么接下来，只需要从**⑨**中筛出平均工资>=8000的就行 => 用**别名替代聚合函数**，再添加这个别名的筛选条件
```sql
mysql> 
select job 
from ( 
	select job, avg( salary ) as avg_salary 
	from emp_table 
	group by job 
) as table2 
where avg_salary >= 8000;

+------+
| job  |
+------+
|    4 |
|    2 |
+------+
2 rows in set (0.00 sec)
```
😄我想这个应该是“平均工资大于 8000 的工作”的正解了



## 3、整理
### （1）整体思路：
要找出“平均工资大于 8000 的工作”，首先明确**目标字段**job、**相关指标**`avg( salary )`、筛选条件`avg( salary ) >= 8000`。于是我们可以先拿到关于目标字段、相关指标的表格，再根据指标**筛选**出符合要求的目标。而要获得这个表格，只需要**根据目标字段进行分组**、**查询目标字段和指标**  

### （2）分步拆解
1. 已知：表1
2. **分组**得到表2：`表1 group by 目标字段`
3. 从分组后的数据取出目标和指标的对应关系（**查询**），得到表3：`select 目标字段, 指标 from 表2`
4. 再根据指标范围**筛选**，得到要求的表4：`select 目标字段 from 表3 where 指标满足筛选条件`

### （3）SQL描述
```sql
select 目标字段 
from ( 
	select 目标字段, 指标 as temp_variable 
	from 表1
	group by 目标字段 
	# 因为执行顺序是先分组，再查询，所以不需要单独括号 ( 表1 group by 目标字段 )
) as table3 
where 筛选条件;
```


## 4、`having`关键字
推导过程的第**⑩**步就是`having`的实现
```sql
select job, avg_salary # 这里可以补充展示平均工资
from ( 
	select job, avg( salary ) as avg_salary 
	from emp_table 
	group by job 
) as table2 
where avg_salary >= 8000;
```

先`from`找到`emp_table`数据源 → 再按job分组 → 然后找出平均工资>=8000的那一组数据 → 查询这组数据的job

```sql
select job, avg( salary ) 
from emp_table 
group by job 
having avg( salary ) >= 8000;
```

---

