##### 文件打包和解包

`tar`<br>

```shell
tar cf /tmp/etc-backup.tar /etc   //打包
```

```shell
tar czf /tmp/etc-backup.tar.gz /etc  //打包的同时进行压缩（得到的文件用tar.gz标识）
```

```shell
ls -lh /tmp/etc-backup.tar*   //查看处理后的文件（主要看大小）
```

```shell
tar xf /tmp/etc-backup.tar -C /root    //解包  -C参数表示存放的位置
```

```shell
tar zxf /tmp/etc-backup.tar.gz -C /root //解包并解压缩
```

##### vim



 





#### Linux权限划分原则

---

思路：

+ 注意权限分离，如系统权限和数据库权限分开
+ 遵循权限最小优先
+ 减少root用户  尽量用“普通用户+sudo ” 进行日常操作
+ 重要系统文件使用chattr锁定，需要操作时再解锁，如 /etc/passwd. /etc/shadow. /etc/fstab./etc/sudoers
+ 使用脚本检测新增的SUID，SGID文件
+ 利用工具检测rootkit脚本
+ 开启SSH密钥对登录方式，修改SSH服务端口



---

##### 

##### 文件基本权限

+ 考虑所有者和所属组

+ 最小化权限原则：用啥权限给啥权限

+ 修改目录或文件权限时，注意递归

  

##### 默认权限

umask



##### 特殊权限

SUID（4）  SGID（2） SBIT（1）



##### ACL权限



##### sudo授权



##### 文件系统属性权限

-a属性   添加属性    

+ 文件只能添加数据，不能删除和修改数据  

+ 对目录添加则只能修改目录下文件的数据，不能建立和删除文件

-i属性 修改属性     

+ 不允许对文件进行删除，改名，也不能添加和删除数据
+ 对目录添加该属性则只允许在目录中建立和修改文件，但是不允许删除

lsattr  查看

chattr 设置

---

#### Linux系统备份策略

思路：

+ 每日备份的数据（异地备份）
  + MySQL数据库（主从备份之外，增量备份一次）
+ 每周备份的数据（异地备份）
  + MySQL数据库（完整备份）
  + 重要系统任务
  + 网页数据
  + 其他服务相关数据



##### 重要系统目录

+ 通用
  + /etc
  + /home
  + /root
  + /var/spool/mail
  + /var/spool/cron
  + /var/spool/at

+ MySQL数据库
  + RPM安装方式的话：/var/lib/mysql
  + 源码包安装的话：/usr/local/mysql/data
+ Apache服务器
  + 网站内容：/var/www/html
  + 配置文件：/etc/httpd/conf/httpd.conf
  + 日志文件：/var/log/httpd
+ 其他服务
  + 该服务的数据文件，配置文件，日志文件



##### 备份策略

+ 完整策略
  + 实现命令：cp  、tar、  dump（ext4文件系统，如CentOS6）、xfsdump（xfs文件系统，如CentOS7）
+ 增量备份
  + 每次备份以上一次备份作为参照，只备份新增内容
  + 实现命令：dump（ext4文件系统，如CentOS6），xfsdump（xfs文件系统，如CentOS7）
+ 差异备份
  + 每次备份以第一次备份作为参照，备份新增内容
  + 实现命令：dump（ext4文件系统，如CentOS6），xfsdump（xfs文件系统，如CentOS7）



##### 备份频率

+ 实时备份
  + 如MySQL主从同步
+ 定时备份
  + 每天，每周备份，一般通过“脚本+定时任务”完成



##### 备份存储位置

+ 基本原则：
  + 本地备份：个人使用
  + 异地备份：企业使用



##### 日志文件备份策略

日志文件时纯文本文件，每天都会产生，超大文本文件难以打开，所以备份时需要进行一定的处理。

+ 日志切割和轮替
  + 系统日志管理工具：logrotate
+ Apache服务配置文件自带日志切割功能，但是需要通过脚本进行轮替



---

#### Linux系统变慢问题定位

思路

总：整机

分：CPU，Memory，Disk，磁盘I/O，网络I/O



##### 整体

- top
  - 主要查看%CPU，%MEM，load average（1，5，15分钟的系统负载，平均值大于0.6就有问题）
  - -h
- uptime
  - 查看机器运行时间，查看load average



##### CPU

+ vmstat 
  + -n 2 3       含义：每两秒采集一次，共采集三次
  + 主要查看头部的procs及尾部的cpu指标
    + procs：包括r  和 b    r代表running process个数，b代表blocked process个数
    + cpu： 包括us，sy，id，wa，st  分别代表user，system，idle，wait，state  若us+sy大于80%，则存在cpu不足情况
+ mpstat -P ALL 
  + 查看多核cpu 所有cpu的使用信息

+ pidstat -u 1 -p <PID>  -r <采样间隔时间>
  + 查看某个进程占用的cpu信息



##### Memory

+ free
+ pidstat
+ /proc/meminfo



##### Disk

+ df -h  
  + 查看磁盘剩余空间
+ du -h
  + 每个文件所占空间



##### 磁盘I/O

+ iostat -xdk 2 3
  + 



##### 网络I/O

+ ifstat

---

#### CPU占用过高原因分析

思路：

+ 使用top命令查找占用最高的非系统进程
+ ps -ef/-aux
+ 定位到具体线程







