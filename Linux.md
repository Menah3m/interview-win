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

##### 网络管理

+ 网络状态查看

```shell
net-tools(centOS7之前)
 ifconfig 查看网卡信息
 mii-tool 网卡名称   查看网卡的物理连接状况
 route -n 查看网关   -n表示不解析ip地址
 
iproute(centOS7及以后)
 ip
 ss
```

+ 网络配置

```shel
设置IP地址
ifconfig <接口> <IP地址> netmask 

ifup  eth0
ifdown eth0

添加路由
route add default gw <GW>  添加默认路由
route add -host <IP> gw <GW>  添加特定主机路由
route add -net <指定网段> netmask <子网掩码> gw <GW> 添加特定网段路由

修改网关
1.先删除  route del default gw <default gw>
2.添加新网关 route add default gw <new default gw>

```



##### 网络排错常用命令

+ ping              检查网络是否连通

+ traceroute   查看到某地址的路径
+ mtr                => my traceroute
+ nslookup     DNS相关
+ telnet            检查端口是否畅通
+ tcpdump      检查数据包

```shell
tcpdump -i any -n port 80   捕获80端口的所有tcp数据包   -n代表：数据包使用ip的形式显示
tcpdump -i any -n host 10.0.0.1    捕获主机10.0.0.1主机的所有tcp数据包
tcpdump -i any -n host 10.0.0.1 and port 80  -w  file_name   -w表示保存结果到某个文件
```

+ netstat          查看端口状态

```shell
netstat -ntpl    -n 使用ip形式显示  -t tcp协议  -p 进程  -l  查看状态是listen的
```

+ ss                    和netstat类似

```shell
ss -ntpl   
```

##### 网络服务管理

> 网络服务管理程序：SysV、systemd

> 网络配置文件：
>
> + 网卡配置：/etc/sysconfig/network-scripts/ifcfg-**（后缀随实际情况变化)
> + 网络参数配置：/etc/hosts

```shell
//查看网络服务状态
systemctl status NetworkManager   
systemctl status network
# network和NetworkManager互斥使用，使用一个就要禁用另一个
//禁用网络服务
chkconfig --level 2345 network off  2345是指系统服务的执行等级
systemctl disable NetworkManager
// 启用
systemctl enable NetworkManager
// 修改主机名
hostnamectl set-hostname 主机名
# 修改主机名后要同时修改 /etc/hosts文件下的对应的主机名关系
vim /etc/hosts
127.0.0.1 原主机名 原主机名 -> 127.0.0.1 现主机名 现主机名
```



##### 软件安装

+ 软件包和包管理器
  + rpm是RHEL系(RHEL\CentOS的软件包格式，对应的包管理器是yum
  + deb是Debian系（Debian\Ubuntu）的软件包格式，对应的包管理器是apt

+ 软件包格式

```shell
vim-common-7.4.10.el7.x86_64.rpm
软件名称-软件版本.系统版本.平台
```

+ rpm命令

```shell
rpm -q 软件包名称
rpm -qa | more    -q 查询软件包  -a 范围是所有已安装    | 管道符     more 分页显示
rpm -i 软件包名称     安装软件包
rpm -e 软件包名称     卸载软件包

```

缺点：安装软件包时无法解决依赖问题， 即要按依赖顺序安装<br>

+ 刻录系统镜像

```shell
dd if=/dev/sr0 of=/xxx/xxx.iso  将光盘里的系统刻成镜像文件
```

+ 挂载mount

```shell
mount  /dev/sr0 /mnt   //将光盘设备挂在到/mnt下
```

##### 用户和用户组管理

+ 用户相关

```shell
useradd  新建用户
userdel  删除用户  -r 删除用户的同时删除家目录  不加-r则不会删除家目录（为了防止误删除数据）
passwd   修改用户密码
usermod  修改用户属性
chage    修改用户密码过期信息
id +用户   查看用户属性
```

+ 用户组相关

```shell
groupadd 新建用户组
groupdel 删除用户组
usermod -g group名 user名  修改user的group
```





##### 用户和用户组配置

###### 用户配置文件：/etc/passwd<br>

```shell
user1:x:1001:1001::/home/user1:/bin/bash
```

共有7个字段:<br>

用户名称 : user1<br>

是否使用密码进行验证(x表示需要) :x<br>

用户id :1001<br>

 组id :1001<br>

 注释 ：无<br>

用户家目录 ：/home/user1<br>

默认登陆shell：/bin/bash（/sbin/nologin表示禁止用户登录）<br>

###### 密码配置文件: /etc/shadow

###### group配置文件：/etc/group

```shell
users:x:100:(空)
```

共有4个字段：<br>

组名：users<br>

是否验证登录：x<br>

组id：100<br>

其他组设置:<br>

 

##### grub

+ grub配置文件
  + /etc/default/grub  简单配置
  + /etc/grub.d/         详细配置
  + /boot/grub2/grub.cfg    grub2的配置文件

```she
grub2-mkconfig -o /boot/grub2/grub.cfg  修改配置文件后使用此命令来产生新的grub配置文件

```

##### 忘记root密码的情况下如何重置

1. 引导界面下进入编辑模式
2. 添加`single`或者`rd.break`，然后`ctrl+x`进行重启
3. `mount -o remount,rw /sysroot`重新挂载（根目录`/`只是内存中的虚拟根目录，硬盘上的目录`/sysroot`才是实际的根目录）
4. `chroot`修改根目录
5. `echo 123456 | passwd --stdin root`重置root密码



##### 进程管理

+ 进程查看

```shell
ps
ps -e    显示所有进程信息
ps -ef   显示所有进程的详细信息（命令的路径等）
ps -eLf  除了进程信息之外，增加了线程信息的显示(LWP-LightWeightProgress)

pstree
top     动态查看系统状态以及进程状态
		按数字键可以单独查看单核cpu占用
		按s可以调整动态更新的频率
top -p [PID] 单独显示对应PID的进程信息
cd /proc/[PID]  可以查看对应PID进程的各种文件
```



+ 进程控制

  + 调整优先级

    nice    范围从-20到19，值越小优先级越高，分配资源越多

    renice 重新设置优先级

```shell
nice -n 10 [filename]
renice -n 15 [PID]
```

+ + 作业前后台的切换

​		jobs

​		&    后台运行

```shell
jobs    显示当前在运行的进程
	fg [job number] 将对应工作编号的进程调至前台
```

​		ctrl+z  将当前前台运行的进程挂起

+ 守护进程（daemon）
  + 不需要打开终端就可以运行的进程，相当于Windows中的服务
+ nohup
  + nohup命令使得进程忽略hangup信号(即使关掉终端，进程也可以正常运行 ）



##### 系统日志

文件位置：/var/log

查看方式：tail -f  [filename]

+ messages   系统日志
+ dmesg     内核信息日志
+ secure     安全日志
+ cron         计划任务日志



##### 服务管理工具 systemctl

+ 服务（提供常见功能的守护进程）集中管理工具

  + service

    > service的启动脚本： /etc/init.d/ 下

  + systemctl

    > systemctl的启动脚本目录：/usr/lib/systemd/system/ 下
    
    ```shell
    systemctl start|restart|stop|enable|disable|reload [服务名称]
    start 开启服务
    restart 重启服务
    reload  不关闭服务重新载入
    stop  关闭服务
    enable  开机自启
    disable 开机不自启
    ```
    
    

+ 原生的1号进程init是系统启动时负责系统服务管理的进程，被systemctl所取代

  ```shell
  chkconfig --list 查看启动级别
  init 0  关机
  init 6  重启
  init 1  单用户
  init 2  不带网络的多用户模式
  init 3  命令行模式
  init 5  图形模式
  init 4  未使用
  ```

+ 启动级别决定服务是否开启

  某些级别下才会开启或者禁用某些服务

  ```shell
  systemctl list-unit-files   使用systemctl列出使用的服务
  ```


+ systemctl下查看级别

  ```shell
  cd /lib/sytemd/system
  ls -l runlevel*.target   查看各个级别的详细文件
  systemctl get-default    查看当前的默认级别
  systemctl set-default 级别名   重新设置默认级别
  ```

+ 服务启动顺序

  ```shell
  cd /usr/lib/systemd/system/
  ls -l  查看当前所有的服务相关文件
  vim sshd.service   查看某个服务的文件（以sshd为例）
  服务的启动顺序由 [unit]块 决定
  ```




##### SELinux

+ MAC（强制访问控制） 与 DAC（自主访问控制）
+ 查看SELinux的命令
  + getenforce	
    + 查看SELinux 的状态：三种 enforcing   permissive  disable（生产环境一般关闭）
    + 临时修改SELinux状态命令：setenforce 0
  + /usr/sbin/sestatus
  + ps -Z      进程相关
  + ls -Z       文件相关
  + id -Z       用户相关
+ 关闭SELinux
  + setenforce 0
  + /etc/selinux/sysconfig
+ SELinux配置文件位置：/etc/selinux/config



##### 内存和磁盘管理

+ 内存和磁盘使用率的查看

  ```shell
  内存查看
  top
  
  free
  free -m 以兆为单位显示
                total        used        free      shared  buff/cache   available
  Mem:           1829        1021         116           2         692         650
  Swap:          1024         264         760
  
  查看磁盘信息
  fdisk -l 
  parted -l    parted -d   
  
  df -h  查看分区信息
  ls -l [directory] 查看某个目录的大小(包含空洞文件-相当于占位置但没有内容，占用的地址计算在内)
  ls -lh [directory] 查看大容量目录的大小(包含空洞文件)
  du  -h [directory]  查看目录或文件的实际大小
  
  
  ```

+ ext4 文件系统

  + centOS6使用
  + centOS7使用xfs文件系统
  + 基本结构：
    + 超级块（包含文件数量等各种信息）
    + 超级块副本
    + inode（记录每一个文件的编号，大小，权限等）
    + datablock

+ 

+ 磁盘配额的使用

+ 磁盘的分区和挂载

+ 交换分区的查看和创建

+ 软件RAID的使用

+ 逻辑卷(LVM)管理

+ 系统综合状态查看







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







