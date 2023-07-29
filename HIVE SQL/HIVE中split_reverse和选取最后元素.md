# HIVE中切分数据split by以及用reverse函数选取最后一个元素
本文借鉴内容：1.https://www.cnblogs.com/bai-yue-kui/p/16502559.html

## 总结

### （一）背景
在经分会三的任务过程中，在前期被放弃的一个方案中，使用了concat不同时间的行程数据（已排序），然后拿取最新一天行程记录的方法，没有使用row number over的方法，而是在concat排序后的数据之后用split和reverse函数来拿取字符串。
***


### （二）Split函数
语法: split(string str, string pat)   
返回值: array   
说明: 按照pat字符串分割str，会返回分割后的字符串数组   
```sql
hive> select split('abcdef', 'c') from test;
--结果：["ab", "def"]
```
用法2，一些经常用到的用法，特殊字符分隔
```sql
hive> select split('a_b_c_Wave2_d-S3','_');
----结果：["a","b","c","Wave2","d-S3"]

hive> select split('a,b,c,d',',');
----结果：["a","b","c","d"]
```
用法3，有些特殊字符需要加转义
```sql
hive> select split('192.168.0.1','\\.');
----结果：["192","168","0","1"]
```
用法4，选取特定结果字符串
```sql
select split('abcdef', 'c')[0];
----结果：ab
```
***


### （三）用Split函数方法取字符串最后一段结果
```sql
--下面这种方法是不行的
select split('a_b_c_Wave2_d-S3','_')[-1];


--可以用下面这种方法
select split('a_b_c_Wave2_d-S3','_')[size(split('a_b_c_Wave2_d-S3','_'))-1];
----结果：d-S3
```
***


### （四）用Split和reverse函数方法取字符串最后一段结果
因为reverse是反转整个字符串，所以最后还要反转一次
```sql
select reverse('a_b_c_Wave2_d-S3'); --结果3S-d_2evaW_c_b_a
select split(reverse('a_b_c_Wave2_d-S3'),'_')[0];--结果3S-d
select reverse(split(reverse('a_b_c_Wave2_d-S3'),'_')[0]);--结果d-S3
```
***
