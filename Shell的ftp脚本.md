# Hive中建立外部分区表
本文借鉴内容：https://blog.csdn.net/zzzzcs/article/details/23993165

## 总结
ftp拿文件脚本有固定模式的范例代码，检查好后使用就行。

## 背景
pdd用户拦截任务中，需要从ftp服务器拿数据文件到己方地址，把相关代码写成一个shell脚本，在NIFI流程里调用。

### （一）ftp脚本批量下载

```shell
#####从ftp服务器上的/data/localacc 到 本地的/data/localacc####
#!/bin/bash
ftp -n<<!
open 10.0.129.170
user sfhc sfhc1234
binary
cd /data/localacc
lcd /data/localacc
prompt
mget *.gz
close
bye
!
```

+ -n是不使用ftp的自动登录 
+ open host[port]：建立指定ftp服务器连接，可指定连接端口
+ user username password：向远程主机表明自己的身份，需要口令时，必须输入口令，如：user anonymous my@email
+ binary：设置文件传输模式为binary，缺省为ascii。binary模式不会对数据进行任何处理，ascii模式会将回车换行
+ 转换为本机的回车字符。关于binary和ascii传输模
+ 式的区别请看： FTP的ASCII和Binary传输模式
+ cd remote-dir：进入远程主机目录remote-dir
+ lcd local-dir：将本地工作目录切换至local-dir
+ prompt：设置多个文件传输时的交互提示（默认为提示，批量下载在每个文件都会提示，输入Y才能进行下一文件的传输，prompt可取消交互提示）
+ mget remote-files：下载多个远程文件
+ close：中断与远程服务器的ftp会话
+ bye：退出ftp会话过程
+ !：在本地机器执行交互shell，exit回到ftp环境
   
+ 单用一个get命令只可以拿到一个文件，mget可以批量下载

### （二）执行sh脚本和shell脚本注释
执行.sh文件，可以使用bash、sh，因为sh是一个链接，链接到bash，例如：
```shell
sh /home/etl/pdd_k_voice.sh
```
在shell脚本中单行注释用#标记，多行注释用":<<!"   语句块   "!"标记

### （三）ftp脚本批量上传
```shell
####本地的/data/localacc to ftp服务器上的/data/localacc####
#!/bin/bash
ftp -n<<!
open 10.0.129.171
user sfhc sfhc1234
binary
hash
cd /data/localacc
lcd /data/localacc
prompt
mput *.*
close
bye
!
```
+ hash：每传输1024个字节，显示一个hash符号（#）
+ mput local-files：将多个文件传输至远程主机

### （四）将脚本放入crontab定时任务
执行命令：```crontab -e```

按 i 编辑文件，添加命令如下：

```0 0 * * * sh /home/getFile.sh```

0 0 * * *指每天0点0分开始执行该定时任务

```sh /home/uploadFile.sh```指执行 home目录下的脚本getFile.sh

按ESC返回命令行模式，再按:wq回车保存并退出。
