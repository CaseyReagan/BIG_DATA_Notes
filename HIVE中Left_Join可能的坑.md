# HIVE中使用Left Join的时候可能会遇到的坑
写法有问题的话，取数可能会丢失左表的数据.   
本文借鉴内容：https://blog.csdn.net/KoHsin_/article/details/116663313.   

## 总结
+ （1）做Left join的时候如果右表有筛选选项，那么筛选的条件要写在ON 的后面，如果写在select的where筛选条件下，那么就会变成inner join(join)，就会丢失左表中null的数据.

### 发现的问题现象
做MNP项目取数的时候发现一个问题。
```sql
----两个表left join----
----HIVE里这种写法会丢失左表的null数据----
SELECT M.user_id, M.mnp_opr_cd, S.src_cd_desc
FROM IT_BI_PRD_EDS.TW_POST_DAILY_MNG_DISC_D M
LEFT JOIN IT_BI_PRD_REF.tr_f_std_code_map S
ON M.mnp_opr_cd = S.src_cd
where S.src_col_nm = 'MNP_Opr_Cd' AND M.tx_dt like '202001%';
----用下面这种写法，不会丢失左表的null数据----
SELECT M.user_id, M.mnp_opr_cd, S.src_cd_desc
FROM IT_BI_PRD_EDS.TW_POST_DAILY_MNG_DISC_D M
LEFT JOIN IT_BI_PRD_REF.tr_f_std_code_map S
ON M.mnp_opr_cd = S.src_cd AND S.src_col_nm = 'MNP_Opr_Cd'
where  M.tx_dt like '202001%';
```

### 问题原因一
见借鉴内容的链接，其他见总结。

### 问题原因二
在HIVE中：   
1. 对于a join b，无论过滤条件放到on中还是where中，结果相同。
2. 对于a left join b，过滤条件在on中：忽略a表的过滤条件，b表的过滤条件只会导致不满足条件的记录在b表的所有字段上都为空；过滤条件在where中：过滤掉所有不满足条件的记录。
3. 核心：left join时on中条件无过滤记录作用，后做where条件过滤记录。on 优先级高于 where。
4. 单纯两表join的话，先单表过滤，在join。原因：查询在map端，进行了数据过滤，shuffle阶段数据更少，join在reduce端。
5. 先两表过滤再join,shuffle阶段数据更多。查询和过滤都在reduce端。
