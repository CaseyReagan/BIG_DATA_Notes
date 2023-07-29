# HIVE中实现group_concat用collect_list(set)及他们的排序
本文借鉴内容：1.concat的几个函数，https://blog.csdn.net/weixin_43342981/article/details/85109781
2.collect_list 和 collect_set函数, https://blog.csdn.net/LINBE_blazers/article/details/89198019
3. collect_list的排序，https://blog.csdn.net/weixin_45366499/article/details/120959728

## 总结
+ 

### （一）背景
做经分会三任务的SQL取数时，涉及一个查询用户4个月的漫游记录，从而得到这个人的漫游轨迹（到过哪些国家）的记录，用来做分析，由于需要根据用户的标志字段，来合并用户的行程记录，同时也是方便导出CSV进入python进行处理分析，所以想到使用mysql中的group_concat函数。然而HIVE sql中有别的函数来做这件事。具体代码逻辑和任务描述见经分会三Readme。
***

### （二）concat函数和 MySQL中的group_concat函数
(1) Concat函数在hive sql和mysql中都可以用来拼接字符串，可以将两个字符串拼接，也可以将两个字段按照一定格式拼接。
```sql
concat(‘a’,‘b’,‘c’) ---- ‘abc’
concat(’‘a,null,’'c)----null
```
(2) Group_concat函数，多列拼接，多列多行值按照某个字段group by以后合并成单行单列显示。

```sql
'''
数据
id|name
1|bob
1|anna
1|helen
2|tom
2|baby
2|tom
'''
--按id分组，把name连接为一行
select id,group_concat(name)
'''
1|bobannahelen
2|tombabytom
'''
--按id分组，把name连接为一行，并按name升序
select id,group_concat(name order by name asc)
'''
1|annabobhelen
2|babytomtom
'''
--按id分组，name去重并连接为一行，按name升序，用逗号分隔
select id,group_concat(distinct name order by name asc separator ‘,’)
'''
1|anna,bob,helen
2|baby,tom
'''
```
***

### （三）collect_list 和 collect_set函数
这两个函数都是将分组中的某列转为一个数组返回，不同的是 collect_list 不去重而 collect_set 去重。用法是 collect_list 一个目标字段然后group by想要按照的某列字段（比如用户id），
```sql
--hive中一张测试表 dual
'''
col1	col2
A	1
B	2
A	3
B	4
C	5
A	3
'''
--用list方法
select col1, collect_list(col2) from dual group by col1;
--结果
'''
A	('1','3','3')
B	('2','4')
C	('5')
'''
--用set方法
select col1, collect_set(col2) from dual group by col1;
--结果
'''
A	('1','3')
B	('2','4')
C	('5')
'''
```
***

### （四）可以结合一个concat_ws来合并数组内容
concat_ws()函数可以把字符串或者上一步得出的数组按照一定的分隔符来进行合并。
```sql
--比如在上面list的例子中如果我们这样写
select col1, concat_ws('-',collect_list(col2)) from dual group by col1;
--结果
'''
A	1-3-3
B	2-4
C	5
'''
```
这样按照分隔符合并之后的字符串格式显然更方便我们输出csv之后进入python的处理
***

### （五）两种函数的选择，以及collect_list内容如何排序
当我们只想看到某些内容，而不是全部内容时，我们就可以用collect_set函数，比如在经分会任务中，如果我们只想看到用户漫游过哪些国家，而不想看到他的整个行程记录，就可以用collect_set函数直接展示出去重之后的国家名字。但是当我们想要获取他整个行程记录用来做进一步分析用的话，就需要用collect_list函数来获得合并后的详细记录，并且最好是按照一定的顺序排列（比如这里可以按照时间升序或者降序，可以完整表示他漫游走过国家的行程记录），因此，对collect_list内容的排序就非常重要。   
一开始试图在collect_list之前建立一个排序的临时表，使用了order by函数，结果最后并没有效果，可能原因是order by只是在查询出结果之后按照一定的顺序进行展示打印，他本身并没有改变数据存储里的顺序。一个最好的方法是，使用带有DISTRIBUTE BY和SORT BY语句的子查询语句来对临时表排序，
例如：
```sql
select
    uid,
    collect_list(fans_uid) as fans_uid_list
from (
	select 
	    uid
	    ,fans_uid
	    ,score
	from tb_user_fans distribute by uid sort by uid,score desc 
)
group by uid
```
这里就按照uid和score来完成了排序,可参考经分会代码，用用户id和时间来排序。
***