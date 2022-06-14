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


