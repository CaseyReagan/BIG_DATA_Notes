# 同id多条数据只取一条row_number()索引
本文借鉴内容：1.https://blog.csdn.net/xiaoleilei666/article/details/114791200

## 总结
+ 用rou_number() over函数非常好用，建立索引并取一条

### （一）背景
经分会三任务中，有些用户有多个号码，排除掉不符合的号码后，多号用户依然存在，此时只需要一个用户随便取一个号用作匹配，所以产生这种，同一id有多条数据，只取其中一条的情况。
***

### （二）HIVE中建立索引并partition的方法
在My sql中处理这种问题的思路，就是建立索引，并取其中一条，这个方法在HIVE中也适用，采用如下语法实现。
```sql
ROW_NUMBER() OVER(PARTITION BY COLUMN ORDER BY COLUMN)
```
```sql
'''
样例数据：
mdn	imei	imsi	vprovId
1064948930129	8670120344055714	460111128580323	123
1064948930129	8670120344055714	460111128580323	123
1064948605404	8666660207679900	460110662972417	234
需求：表数据按照手机号去除重复，每个手机号只保留一条记录
'''

select *
from(
     select *,row_number() over (partition by mdn order by vprovId asc ) num from aa_country_analysis
    ) t
where t.num=1; 
```
row_number()从1开始，为每一个分组记录返回一个数字，这里的ROW_NUMBER() OVER (ORDER BY visit_province_id asc) 是先把visit_province_id列升序，再为升序以后的每条mdn记录返回一个序号。最后选择序号为1的记录就行。   
【注意】序号编号是从1开始的