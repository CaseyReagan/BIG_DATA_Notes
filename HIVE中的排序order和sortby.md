# HIVE中的两种排序方式order by和sort by
本文借鉴内容：1.关于order by，https://blog.csdn.net/AnameJL/article/details/112413521
2.

## 总结
+ 全局排序用order by，分组排序用distribute by sort by更快

### （一）背景
Hive里有两种排序方式，order by 和 sort by，那么他们有什么区别呢。
***

### （二） HIVE中的 order by
在使用order by的时候默认是按照升序进行排序的(ASC)，字符串类型就是按照字典顺序进行排序的，数值类型就是按照数值的大小进行排序的。语法：
```sql
SELECT * FROM tab_name ORDER BY column_name;
```
想要做降序的话就在列名后面加上desc.   
***

### （三）其实order by可以做多列排序
有的时候也会使用到多字段进行排序，在使用多字段排序的时候，我们需要在order by后的每个字段后面都注明排序方式，具体语法如下：
```sql
SELECT * FROM tab_name ORDER BY column_1 desc,column_2,desc,column_3....
```
多字段的排序规则是先将第一个出现的字段按照正序或者倒序进行排序，在排序好之后，在第一个字段值相同的情况下第二个字段在以正序 或者倒序的方式的进行排序，可以理解位以第一个字段值做分组再以第二个字段进行排序.
```sql
select
 goods,
 case gtype
 when '食品'
 then concat("7_",gtype)
 when '日用品'
 then concat("3_",gtype)
 when '电器'
 then concat("5_",gtype)
 end gtype
 ,
 price
from t_test
order by gtype,price desc;
```
【重要】这个很有意思，用case when加上多列order by排序，可以做到先分组再排序。结果：
```sql
goods	gtype	price
椅子	3_日用品	91
水壶	3_日用品	81
水杯	3_日用品	71
卫生纸	3_日用品	61
毛巾	3_日用品	51
拖布	3_日用品	41
洗洁精	3_日用品	31
洗衣液	3_日用品	21
洗衣粉	3_日用品	11
热水器	5_电器	102
空调	5_电器	92
电磁炉	5_电器	82
电暖器	5_电器	72
小太阳	5_电器	62
洗衣机	5_电器	52
冰柜	5_电器	42
冰箱	5_电器	32
电脑	5_电器	22
电视	5_电器	12
方便面	7_食品	90
月饼	7_食品	80
面包	7_食品	70
饼干	7_食品	60
薯条	7_食品	50
蛋糕	7_食品	40
巧克力	7_食品	30
薯片	7_食品	20
口香糖	7_食品	10
```
***
### （四）order by是全局排序
order by是进行全局的排序，所以最终数据都会集中在一个reduce中，所以慢，并且在hive使用order by的时候会受到如下属性的约束：
```sql
set hive.mapred.mode=nonstrict;
set hive.mapred.mode=strict;

--默认是在nonstrict模式下的，如果在strict模式下使用order by的话必须使用limit关键字，因为如果数据量过大的话，执行的时间会非常长。
```
如果在strict模式下，不加Limit执行order by，会报错“Order by-s without limit”。
***
### （五）sort by和distribute by
(1)sort by是局部排序。减去了order by最后一个归并排序，只要各个机器节点里的数据有序就行了。Sort by为每个reducer产生一个排序文件。每个Reducer内部进行排序，对全局结果集来说不是排序。我们做sort by的时候可以手动给reducer设置个数。
```sql
hive (default)> set mapreduce.job.reduces=3

--查看设置reduce个数
hive (default)> set mapreduce.job.reduces;
```
默认情况下set mapreduce.job.reduces=-1，见记录"HIVE中set reduces个数.md"   
(2)distribute by决定了再sort by的时候分成几类，比如n条数据，被分成n/m条数据，那么复杂度就是  (n/m)*(m)*log(m)=nlogm，分的越离散(m越小)，速度越快，因为不需要最后做归并排序(m越小，数据条数就越多，归并排序的时候就越耗时)，关于n条如何被分为m条，需要用distribute by指定字段，比如指定name，这样就可以保证相同name的数据有序了。比如在经分会三中，根据imsi或号码来distribute然后按照时间来sort by。
***

### （六）HIVE中cluster by
在《Hadoop权威指南第二版》中这样描述道：如果sort by和distribute by中所用的列相同，可以缩写为cluster by以便同时指定两者所用的列
***
