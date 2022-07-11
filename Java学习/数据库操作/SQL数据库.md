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

