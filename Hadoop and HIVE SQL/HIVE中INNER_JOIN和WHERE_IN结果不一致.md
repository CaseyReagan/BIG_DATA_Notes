# HIVE中INNER_JOIN和WHERE_IN结果不一致
本文借鉴内容：1.https://www.656463.com/wenda/SQLINNERJOINyWHEREIDINbyydjg_315

## 总结
+ 尽量用where in操作，不要用inner join，即便是使用where in会变成两步操作，也比无意识状况下导致数据出错好。
+ 如果要做INNER JOIN一定保证右表join on的字段的数据是唯一的（加distinct无用）
+ 用A left join B on xxx and B.xxx 方法可以一次性做完 

### （一）背景
在做家宽百万用户地址匹配任务的时候，遇到一个场景：要将匹配出来的用户楼宇地址，匹配公司已有宽带部署的楼宇地址，因为需要将A表 Left join B表后，拿取B表的某个字段，所以需要进行两步操作，先left join再 where in，于是乎想用inner join来一次性操作，因为INNER JOIN取二者交集的同时，也可以合并表内容。
***

### （二）问题
在做了操作后，惊讶的发现在INNER JOIN后的数据表的数据量，比原来的A表还要多。但是做交集难道不该更少吗？于是乎，用left join后 where in筛选了一次，发现数据量和做INNER JOIN不相同。遂检查该问题。
***


### （三）问题原理
where in的逻辑是，根据右表选定的字段条件，筛选左表，只是一个select筛选的过程，只可能减小或者不变左表数据量，假如左表有多条数据匹配上了右表筛选的条件，那么左表这个多条数据都会保留，这种方法是最稳妥的。（但是不能联结两表数据，所以需要在where in之前多做一步join）    

而inner join，与left join等表联结操作一样，是要按字段匹配的。INNER JOIN将为每个成功的连接选择父表中的行一次，因此如果子表中有3行的值匹配上父表，父表中匹配上的行数为2，则结果中会有6行值。   

要使它们“相同”，要将“DISTINCT”添加到主选择中（但是这样如果多条记录的其他列不相同又无法成功）。所以跟left join一样，如果出现3对2的情况，最后就会有6条记录。
***


### （四）补充一种解决方法
后来发现可以直接在 A left join B的时候，将B表的筛选条件写在 ON的后面，以AND的方式，添加B表的字段筛选条件，这样就可以一步做完。   