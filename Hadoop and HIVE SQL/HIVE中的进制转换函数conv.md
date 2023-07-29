# HIVE中的进制转换函数CONV()

### （一）背景
做基站cell编码转换任务的时候，遇到一个需要在hive sql中做十进制与十六进制转换的需求。详见"cell十进制转16进制"任务readme.
***

### （二）conv()函数的用法
conv()函数，是用来进行进制转换的,Converts a number from a given base to another.   
```sql
conv(num,from_base,to_base)
```
解释：num可以是int型也可以是string类型，from_base表示原来的进制，to_base表示要转成的进制，返回的结果是string类型。如果num有NULL，那么conv的结果也是NULL。
```sql
sql> select conv('7f2a4',16,10);
520868
Time taken: 11.038 seconds, Fetched 1 row(s)
```
