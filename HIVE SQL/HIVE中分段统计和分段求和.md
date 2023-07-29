#  HIVE中分段统计和分段求和
本文借鉴：http://blog.coinidea.com/%E5%A4%A7%E6%95%B0%E6%8D%AE-1079.html

## 总结
+ (1) 利用count里嵌套case when即可简单实现

### （一）背景
有的时候，我们想要对目标列，进行分段统计数量，比如，统计年龄1-18岁，18-30岁，30-40岁……等等年龄分段的人数。   

### （二）方法一 count和case when嵌套
代码举例如下，这是看起来最简单明晰的一个办法，缺点是如果分组很多的话，手写代码很麻烦。
```sql
--统计出text文本长度为[0,20]、[20,40]、[40,60]…按区间划分的数量
select count(case when length(text) between 0 and 20 then 1 end) as text1,
count(case when length(text) between 21 and 40 then 1 end) as text2,
count(case when length(text) between 41 and 60 then 1 end) as text3,
count(case when length(text) between 61 and 80 then 1 end) as text4,
count(case when length(text) between 81 and 100 then 1 end) as text5,
count(case when length(text) &gt; 100 then 1 end) as text6
from table;
```

补充信息，HIVE SQL中的between and 是包头包尾巴的。   

当然也可以不用嵌套，而是分成两部分写，先按case when打上标签，再按标签统计数量。
```sql
case when age>=0 and age<=18 then "0_18"
when age>=19 and age<=24 then "19_24"
when age>=25 and age<=30 then "25_30"
when age>=31 and age<=40 then "31_40"
when age>=41 and age<=60 then "41_60"
when age>=61 then "0_18" end as fenduan
```

### （三）方法二
这种方法，先将每个text的文本长度除以20，向下取整，再乘以20得到一个分组依据，以这个依据进行group by就可以得到最终结果，在规避循环的情况下，用简单的语句，巧妙的解决了自动分区间统计的问题。
```sql
select floor(length(text)/20) * 20, count(*) from table group by floor(length(text)/20);
```


