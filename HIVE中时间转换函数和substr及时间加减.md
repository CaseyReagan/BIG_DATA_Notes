# HIVE中需要用到时间转换的地方太多了

## 总结
+ （1）用unix_timestamp和from_unixtime转换时间。
+ （2）可用substr和concat来截取特殊的时间字符串。
+ （3）


### （一）经常用到的重要函数：substr、substring、concat
因为经常要截取string类型的日期的内容，所以用到这个函数很多。   
语法: substr(string A, int start),substring(string A, int start)。返回字符串A从start位置到结尾的字符串。
```sql
substr(string A, int start,int length)
substring(string A, int start, int length)

select substr('你好明天',0,3) filed;
返回：
你好明
select substr('你好明天',1,3) filed;
返回：
你好明
说明：下标从1开始

select substr('你好明天',1,2) filed;
返回：
你好
select substr('你好明天',2,2) filed;
返回：
好明
select substr('你好明天',2,3) filed;
返回：
好明天
select substr('你好明天',3,3) filed;
返回：
明天

```
concat函数用来拼接字符串，返回结果为连接参数的字符串。如果有一个参数为NULL，则返回的结果为NULL。   
```sql
concat(‘a’,‘b’,‘c’) ---- ‘abc’
concat("a,null,"c) ----null
```
concat_ws('分隔符',str1,str2,…)concat()的一个特殊形式，表示concat with separator，两个参数之间加上特定的分隔符。返回的是用指定分隔符连接参数的字符串。如果分割符为null，则返回null，参数为null，则忽略该参数。
```sql
concat_ws("/,"2018,"12,"19) ----2018/12/19
concat_ws('':,''22,'47',null)   ----22:47
concat_ws(null,''22,'47')----nul
```


### （二）HIVE中常见的关于日期和时间的函数
时间转换的常用函数
```sql
unix_timestamp --:返回当前或指定时间的时间戳
from_unixtime   --：将时间戳转为日期格式
current_date    --：当前日期
current_timestamp   --：当前的日期加时间
to_date     --：抽取日期部分
year    --：获取年
month   --：获取月
day     --：获取日
hour    --：获取时
minute  --：获取分
second  --：获取秒
weekofyear  --：当前时间是一年中的第几周
dayofmonth  --：当前时间是一个月中的第几天
months_between  --： 两个日期间的月份，前-后
add_months  --：日期加减月
date_add    --：日期加天数
date_sub    --：日期减天数
last_day    --：日期的当月的最后一天
datediff    --：两个日期相差的天数，前-后
```
字段取整函数
```sql
round   --： 四舍五入
ceil    --： 向上取整
floor   --： 向下取整
```
字符串操作函数
```sql
upper   --： 转大写
lower   --： 转小写
length  --： 长度
```

### （三）两个最重要时间转换函数
（一）unix_timestamp函数   
Unix时间戳，指的是Unix操作系统规定的从1970年1月1日开始所经过的秒数，不考虑润秒。在该系统下，一小时表示为3600秒，一天表示为86400秒，不考虑闰秒。（这样规定的原因是，Unix操作系统是1969年定型设计的。）   
unix_timestamp函数可以将相应的时间，或者符合格式的string时间形式，转换为Unix时间戳形式。例如，如果参数date满足yyyy-MM-dd HH:mm:ss形式，则可以直接unix_timestamp(string date)得到参数对应的时间戳，或者满足yyyy-MM-dd形式   
```sql
select unix_timestamp();  --返回当前时间的Unix时间戳

select unix_timestamp('2018-12-05 01:10:00','yyyy-MM-dd HH:mm:ss');
--返回该日期的Unix时间戳
```

（二）from_unixtime函数   
就是把Unix时间戳转换成时间格式（date格式）   
```sql
select from_unixtime(1543943400);

select from_unixtime(1543943400,'yyyy-MM-dd');
```

（三）同时使用举例   
20180905转成2018-09-05，   
```sql
select from_unixtime(unix_timestamp('20180905','yyyymmdd'),'yyyy-mm-dd')
from dw.ceshi_data
```

2018-09-05转成20180905   
```sql
select from_unixtime(unix_timestamp('2018-09-05','yyyy-mm-dd'),'yyyymmdd')
from dw.ceshi_data
```

（四）如果遇到很长的字符串格式存储一个时间怎么办   
假设Hive中存了'20170728102031'这样一个string类型字符串。需要将字符串调整为'2017-07-28 10:20:31'，这个时候就要把substr和concat组合起来使用。
```sql
concat(substr('20170728102031',1,4),'-',
substr('20170728102031',5,2),'-',
substr('20170728102031',7,2),' ',
substr('20170728102031',9,2),':',
substr('20170728102031',11,2),':',
substr('20170728102031',13,2))
```


### （四）其他时间转换、加减技巧


