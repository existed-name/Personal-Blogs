# 一道SQL分组过滤题

> 推荐阅读：[推导`having`关键字](/技术探索&评测/试一下手写`having`关键字.md)  
> [个人博客仓库](/数据结构与算法刷题/SQL/一道SQL分组过滤题.md)
---

## 1、题目
（1）查询入职时间在 `'2015-01-01'` (包含) 以前的员工 , 并对结果根据职位分组 , 获取员工数量大于等于2的职位  
（2）已知：数据表`emp_table`，字段`entry_date`、`id`、`job`等等
（3）这里记录不用、用`having`的2种写法

---

## 2、“原始”推导
先拆分问题  

### （1）核心问题：获取员工数量大于等于2的职位——涉及分组+聚合函数，较复杂
#### ①要点
1. **目标字段**：职位job
2. **相关指标**：员工数量`count( * )`或者`count( id )`（不含有为`null`的`id`）、`count( 常量 )`等等，用别名`count`替代
3. 筛选条件：`count >= 2`

#### ②分步拆解
1. 只要得到关于**目标字段及其相关指标**的数据表格，就可以根据要求的指标范围，从里面筛选对应的目标字段
2. 最初表1：数据源`emp_table`
3. 于是根据目标字段对原表**分组**，**统计出每组的指标**，之后就可以筛选了
4. 中间表2：`select 目标字段, 指标 from 表1 group by 目标字段;`
5. 最终表3：`select 目标字段, 指标 from 表2 where 指标达到要求;`
6. **注**：聚合函数的结果、中间产生的表格需要用别名替代

#### ③SQL
```sql
select 目标字段, 指标的别名
from ( 
	select 目标字段, 指标 as 指标的别名
	from 数据源
	group by 目标字段
) as 表的别名
where 筛选指标;

select job, count
from ( 
	select job, count( * ) as count
	from emp_table
	group by job
) as table2
where count >= 2;
```


### （2）次要问题：入职时间直接where条件判断即可
```sql
select * from emp_table where entry_date <= '2015-01-01';
```


### （3）合并
```sql
select job, count
from ( 
	select job, count( * ) as count
	from ( 
		select * 
		from emp_table
		where entry_date <= '2015-01-01'
	) as table2
	group by job
) as table3 
where count >= 2;

# 还可以进一步简化
select job, count
from ( 
	select job, count( * ) as count 
	from emp_table
	where entry_date <= '2015-01-01'
	group by job
	# 执行顺序：from获取数据源 → where初步过滤 → group by分组 → select查询
	# 就不需要单独开个表装初步过滤后的数据了
) as table2 
where count >= 2;

# 那么完整的模板
select 目标字段, 指标的别名
from ( 
	select 目标字段, 指标 as 指标的别名
	from 数据源
	group by 目标字段
	where 初步过滤
) as 表的别名
where 筛选指标;
```

---

## 3、加入`having`
```sql
select 目标字段, 相关指标
from 数据源
where 初步过滤
group by 目标字段		# 按照目标字段分成几组，每组统计出对应的指标
having 指标的约束;		# 筛选出达标的那组

select job, count( * ) as count
from emp_table
where entry_date <= '2015-01-01'
group by job
having count >= 2;
# 某些数据库，`having` 后面不能用别名，改为聚合函数更保险
```

---

## 4、附：SQL执行顺序
> by GPT...
> 1. `FROM` (选择数据源)
> 2. `WHERE` (过滤记录)
> 3. `GROUP BY` (分组)
> 4. 聚合函数 (如 `avg`, `sum` 等)
> 5. `HAVING` (分组后的过滤)
> 6. `SELECT` (选择最终需要的列)
> 7. `ORDER BY` (排序)

---
