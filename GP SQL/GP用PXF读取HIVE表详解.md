#  GP用PXF读取HIVE表详解
本文借鉴：1. Greenplum数据库中文文档 v6.0，https://www.iquanku.com/read/greenplum-admin_guide-6.0-zh/2d7dc2d2dd1593ee.md#d3i2al

## 总结
虽然GP外部表用pxf连接HIVE分区表，load数据的时候不会读取全量分区数据，但是元数据太多的表，依然会比读一个不分区的一日临时表占用更多资源，要么1.在GP外部表建立连接的时候要限制分区，要么2.直接在HIVE里建一个分区的临时表，连接这个临时表。
***

### （一）背景
在Green Plum 5之后，可以使用PXF连接器连接读取HIVE表数据，但是在使用中的时候，发现乙方的同事在连接GP外部表的时候，不通过HIVE一张外部零时表转一次分区数据，而是直接连接的HIVE分区表的信息，这难道不会导致拿全量分区吗？
***

### （二）PXF连接HIVE分区表，分区到底怎么写
在之前的文章《GP外部表连接hdfs的两种方式》中，介绍了如何在GP4和GP5里用两种不同的方式读取HIVE表数据，GP4用gphdfs方式，而GP5之后的版本可以用pxf结构化读取。   

那么到底用PXF连接器，直接连接HIVE分区表（不带筛选条件），到底会造成什么后果呢。检查过后，发现PXF连接HIVE表的时候，location不写分区的话，并不会load全量数据，PXF方式只是去hdfs里读取了该location表的元数据。   
但是，如果像这样每次都要读入全部分区的元数据，并且这样的任务每日执行的很多，且HIVE表很复杂的情况下，还是会消耗GPload更多的资源。因此不要链接整个HIVE分区表更好。方法右二。   
1. 在HIVE每日建立一个一日分区的（或者月表建立一个月的）临时表，将最新分区数据插入这个临时表，这样就将数据ETL的操作放到了Hadoop上，而GP的外部表，只需要链接这个每日的临时表。
2. 采用每日新建GP外部表的时候，location直接精准到日分区目录，相当于把筛选日分区工作的数据放到了GP侧执行，例子如下。可以看到写location的时候，地址没有只到HIVE表名目录，而是到了下级分区目录tx_dt，而分区目录的时间则是一个每日日期的变量，每日自动生成。
```sql
drop external TABLE if exists it_xxx_prd_eis.ext_te_xxx_xxx_dealer_${filedate};
CREATE external TABLE it_xxx_prd_eis.ext_te_xxx_xxx_dealer_${filedate}
(
xxx_cd                   text,
xxx_nm                   text,
xxx_grp                  text,
xxxx_inst_id             text,
xxxx_inst_nm             text
)
LOCATION ('pxf://data/IT/PRD/IT_xxx_PRD_EIS/te_xxx_xxx_dealer/tx_dt=${filedate}?PROFILE=HdfsTextSimple')
FORMAT 'TEXT'(DELIMITER E'\u001E' NULL '' );


TRUNCATE TABLE it_xxx_prd_eis.te_xxx_xxx_dealer;

insert into it_xxx_prd_eis.te_xxx_xxx_dealer
select 
xxx_cd                   ,
xxx_nm                   ,
xxx_grp                  ,
xxxx_inst_id             ,
xxxx_inst_nm             ,
TO_DATE('${filedate}', 'YYYYMMDD')
from it_xxx_prd_eis.ext_te_xxx_xxx_dealer_${filedate};

drop external TABLE it_xxx_prd_eis.ext_te_xxx_xxx_dealer_${filedate};
```
***
