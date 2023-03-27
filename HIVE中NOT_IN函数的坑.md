# HIVE中使用WHERE NOT IN函数的时候遇到的坑

本文借鉴内容：https://blog.csdn.net/weixin_44421339/article/details/96201258.   
(2)https://www.modb.pro/db/114278.   

## 总结
+ （1）在HIVE中做WHERE NOT IN的时候，NOT IN里的子查询数据内容不能有NULL，三种解决办法， 子查询带条件不为空，用nvl()函数套一层处理NULL值为0，或者采取left join之后再count XXX is not null.

### （一）背景及发现的问题现象
在做预付Halt card人数筛选并明细提数项目的时候，一开始只是在数据仓库GP里根据条件筛选，但是后来随着要求筛选的条件和需求越来越多，导致需要将GP上的逻辑在HIVE里再实现一遍，（从而方便从HIVE其他地方拿数做进一步筛选），此时遇到一个问题，GP里count()出来的数据，最后和HIVE中SQL代码count()出来的结果不一致。

### （二）问题原因
在HIVE中，进行in，和not in 的判断中返回的是Boolean类型，但Boolean(null)返回的还是null，当not in() 中的数值包含NULL，匹不上的数据会返回NULL而不是True。
这就是本质原因，NOT IN()判断在HIVE中有三个结果，True,False,NULL.因此统计结果不同。这跟我们日常理解的SQL的 in+not in= all 是不同的。   

### （三）示例
```sql
--not in的原始结果
select num,num not in (null,'2'), num not in (null,'2') and true from
(select explode(split('1,2,3,4',',')) as num)t;

--结果
1   NULL    NULL
2   false   false
3   NULL    NULL
$   NULL    NULL
```
更清晰的逻辑示例内容，见借鉴链接2.

### （四）解决办法
(1). 用nvl(XXX,0) NOT IN (sub querry)方法，将NULL值转为0，之后再匹配就OK.
(2). 在sub querry中，select的时候就过滤掉空值, is not null.
(3). 不做where not in，改为left join之后筛选掉右边相关字段 is not null.(这种方法稳妥，多做一步，但是逻辑和流程清晰，HIVE里left join很快反正)