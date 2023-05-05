## 1. 数据库的基本操作

### 1.1 时间操作

查询指定日期内的数据操作：

```sql
select * from  edu_teacher et where gmt_create > '2020-07-03' and gmt_create < '2022-07-03';
-- 或者
select * from  edu_teacher et where gmt_create between '2019-09-12' and '2022-07-03';
```



```sql
select 
	_main._office_place,
	_main._total_sent,
	_main._total_failed,
	_sub1._delay_avg,
	_sub2._delay_avg,
	_sub3._delay_avg,
	(_sub3._delay_avg - _sub2._delay_avg) as _wan_delta
from (
	SELECT
	agent_info.office_place as _office_place,
	sum( network_status_history.num_success + network_status_history.num_failure ) AS _total_sent,
	sum( network_status_history.num_failure ) AS _total_failed
FROM
	network_status_history
	INNER JOIN agent_info ON network_status_history.mac_address = agent_info.mac_address 
WHERE
	$__timeFilter(network_status_history.created_at)
GROUP BY
	agent_info.office_place
) as _main
left join (
SELECT
	agent_info.office_place as _office_place,
	network_status_history.target_address_type,	
	sum( network_status_history.avg_delay * network_status_history.num_success ) / sum( network_status_history.num_success ) AS _delay_avg 
FROM
	network_status_history
	INNER JOIN agent_info ON network_status_history.mac_address = agent_info.mac_address 
WHERE
	$__timeFilter(network_status_history.created_at)
	and network_status_history.target_address_type = "LAN.AP"
GROUP BY
	agent_info.office_place,
	network_status_history.target_address_type
) as _sub1 on _main._office_place = _sub1._office_place
left join (
SELECT
	agent_info.office_place as _office_place,
	network_status_history.target_address_type,	
	sum( network_status_history.avg_delay * network_status_history.num_success ) / sum( network_status_history.num_success ) AS _delay_avg 
FROM
	network_status_history
	INNER JOIN agent_info ON network_status_history.mac_address = agent_info.mac_address 
WHERE
	$__timeFilter(network_status_history.created_at)
	and network_status_history.target_address_type = "WAN.BAIDU"
GROUP BY
	agent_info.office_place,
	network_status_history.target_address_type
) as _sub2 on _main._office_place = _sub2._office_place
left join (
SELECT
	agent_info.office_place as _office_place,
	network_status_history.target_address_type,	
	sum( network_status_history.avg_delay * network_status_history.num_success ) / sum( network_status_history.num_success ) AS _delay_avg 
FROM
	network_status_history
	INNER JOIN agent_info ON network_status_history.mac_address = agent_info.mac_address 
WHERE
	$__timeFilter(network_status_history.created_at)
	and network_status_history.target_address_type = "WAN.SBC"
GROUP BY
	agent_info.office_place,
	network_status_history.target_address_type
) as _sub3 on _main._office_place = _sub3._office_place
```

### 1.2  EXISTS 



语法：

```sql
SELECT 字段 FROM table WHERE EXISTS (subquery); 

select * from TableIn where exists(select BID from TableEx where BNAME=TableIn.ANAME)
```

> 简单说下你的理解吧
>
> 首先查询外面的，SELECT 字段 FROM table
>
> 假设返回了100条记录
>
> 然后阵这100条记录，执行内部的查询，如果内部查询有值，则返回该条记录，否则不返回该条记录

**EXISTS执行顺序：**

- 1、首先执行一次外部查询，并缓存结果集，如 SELECT * FROM A
- 2、遍历外部查询结果集的每一行记录R，代入子查询中作为条件进行查询，如 SELECT 1 FROM B WHERE B.id = A.id
- 3、如果子查询有返回结果，则EXISTS子句返回TRUE，这一行R可作为外部查询的结果行，否则不能作为结果

**备注**

> SQL的写法很怪异
>
> ```sql
> select * from student s2 where exists (select student_id from student s where s.student_id = s2.student_id and s.student_id > 161002)
> ```
>
> 需要指定外部表和内部表的连接，并且不能都写成student
>
> 
>
> ```sql
> select * from student s2 where exists (select student_id from student s where  s.student_id = 161002)
> ```
>
> 就算这样，也是回返回全部的数据
>
> 此外，如果没有指定连接，那么会先走内部的SQL，这条全部返回
>
> ```sql
> select * from student s2 where exists (select student_id from student s where s.student_id = 161002)
> ```
>
> 





**拓展：**

```sql
INSERT INTO TableIn (ANAME,ASEX) 
SELECT top 1 '张三', '男' FROM TableIn
WHERE not exists (select * from TableIn where TableIn.AID = 7)
```

在插入记录前，需要检查这条记录是否已经存在，只有当记录不存在时才执行插入操作，可以通过使用 EXISTS 条件句防止插入重复记录。

#### 待定：EXISITS 和 IN 的区别与效率差别
