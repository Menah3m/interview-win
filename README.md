### 基础知识总结

---



##### 计算机网络

> 计算机网络体系结构

+ OSI七层模型
+ TCP/IP四层模型

> TCP和UDP

+ 三次握手
  + 第一次：Client将**SYN置1**，**随机**产生一个初始序列号**seq**发送给server，进入**SYN_SENT**状态
  + 第二次：Server收到Client的SYN=1之后，知道客户端请求建立连接，将自己的**SYN置1**,**ACK置1**,产生一个**ack=seq+1**，并随机产生一个 自己的初始序列号，发送给Client，进入**SYN_RCVD**状态
  + 第三次：Client检查ack是否为seq+1，ACK是否为1，检查正确后将自己的**ACK置1**,产生一个**ack=seq+1**,发送给服务器，进入**ESTABLISHED**状态。Server检查ACK为1和ack=seq+1之后，也进入了ESTABLISHED状态。连接建立。

+ 四次挥手
+ 流量控制
+ 拥塞控制
+ TCP和UDP区别
+ TCP如何保证传输的可靠性

> HTTP和HTTPS

+ 区别
+ GET和POST
+ Session和Cookie的区别
+ 从输入URL到获得 页面的整个过程
+ HTTP常见的状态码

>网络层协议

+ IP地址分类

+ 保留地址

+ NAT

+ ARP

+ RIP

+ OSPF

  

##### 操作系统

> 进程和线程

+ 进程和线程的区别
+ 进程间的通信方式
+ 进程同步
+ 进程有哪几种状态
+ 进程调度策略
+ 僵尸进程
+ 线程同步方式
+ IO多路复用及实现方式

> 死锁

+ 什么是死锁
+ 死锁产生的必要条件
+ 死锁的处理方法

> 内存管理

+ 分页和分段的区别
+ 虚拟内存
+ 页面置换算法

> 磁盘调度



##### 数据库

> 数据库基础

+ 事务的概念和特性
+ 会出现哪些并发一致性问题
+ 数据库的四种隔离级别
+ 乐观锁、悲观锁
+ 常见封锁类型
+ 三级封锁协议
+ 两段锁协议
+ MVCC
+ 数据库范式
+ 列举几种表连接方式
+ 什么是存储过程？有哪些优缺点？
+ Drop/Delete/Truncate的区别
+  什么是视图？什么是游标？

> MySQL

+ 数据库索引的实现原理（B+树）
+ 使用索引的优点
+ 哪些情况索引会失效
+ 在哪些地方适合创建索引
+ 索引的分类
+ MySQL的两种存储引擎InnoDB和MyISAM的区别
+ 如何优化数据库
+ 什么是主从复制？实现原理是啥？

> NoSQL/Redis



##### 中间件

+ Nginx



##### 数据结构



##### 运维工具

+ 自动化
  + ansible



----

##### 参考资料

+ [面试常见问题及follow-up](https://github.com/wolverinn/Waking-Up)
+ 



