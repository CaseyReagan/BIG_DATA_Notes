#  Hadoop常见命令

## 总结


### （一）背景
在经常使用hadoop命令过程中，有一些常用的命令做一些总结。按本人常用顺序排列。
   ***


### （二） -ls 命令
查看目录下的文件
```
hadoop fs -ls /
```
以递归方式查看目录下文件
```
hadoop fs -ls -R /
```
具体用法呢，可以结合HIVE SQL命令，show create table，从而获得某张数据表的hdfs地址，然后通过hadoop fs -ls命令就可以直接看到该地址下表格存储的数据，有时候表分区有历史数据没删掉，导致的数据量和数据内容的错误，就可以通过查看ls命令排查。   
   
***


### (三) -get -getmerge 命令
get命令与linux下相同，用来将hadoop中某目录下的的文件，下载到linux下某本地目录下。
```
hadoop fs -get /park/jdk /home

//把hdfs文件系统下park目录的文件下载到linux的home目录下
```
   
getmerge命令更常用，功能与get一样，但是getmerge是将指定目录下所有文件合并在一起，下载到本地linux目录，这个用的更多是因为，当数据量特别大的时候，有些分区表的分区目录下，会把数据拆成好几个CSV存储，这时用getmerge命令就合并成了一个。或者，在一个D/H/M15的分区的表下，我想拿某小时的数据，就可以直接合并该目录下所有M15目录下的文件。
```
hadoop fs -getmerge /park /root/tmp

//将park目录下的所有文件合并成一个文件，并下载到linux的root目录下的tmp目录
```
   
***
   
### (四) -put 命令
上传文件进hadoop存储系统下，偶尔会用到，几乎用不到，古早做法会上传文件到分区目录下，然后执行hive Sql命令挂载该文件到该外部表下。偶尔可以用来大批量导入数据。
```
hadoop fs -put /root/a.txt /park

//将linux操作系统root目录下的a.txt放在hdfs的park目录下
```
   
***
   

### (五) -rm -rmdir -rnr 命令
删除目录和文件的三个命令，慎用，除非是删除外部表的时候，几乎用不到，不得乱用。
```
hadoop fs -rm /park/文件名
//删除hdfs的park目录的指定文件

hadoop fs -rmdir /park
//删除park目录，但是前提是目录下没有文件

hadoop fs -rmr /park
//递归删除park目录及目录下所有文件。
```
   
***

### (六) -du 命令

### () -cat -tail 命令
与linux中相同，查看目录下文件的内容。该命令没什么用，需要查看内容来检查数据一般都会下载到本地仔细检查。

### () -mv -cp 命令
与linux相同，mv可以移动文件和重命名文件，cp命令用来复制文件（注意不是下载），从没用过，不详写。