# HIVE中需要用到时间转换的地方太多了

### （一）经常用到的一个重要函数：substr、substring
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



