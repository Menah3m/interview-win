#### Nginx知识整理



##### Nginx 

engine X的简称，用来解决较大量并发连接数的Web服务器程序



##### c10k问题

并发连接超过10000



##### 特性

+ 扩展性：有很多第三方模块
+ 高可靠性：master/worker模式
+ 支持热部署：版本之间可以在不停止运行的情况下平滑过渡
  + 不停机更新配置文件
  + 不停机更换日志文件
  + 不停机更新版本
+ 低内存消耗：10000个keep-alive连接模式下的非活动连接只需要2.5MB内存
+ 支持异步，事件驱动



##### 功能

+ 静态资源的Web服务器
+ Http协议的反向代理服务器
+ pop3/IMAP协议反射代理服务器
+ 支持FastCGI，uWSCGI等协议
+ 模块化机制，著名模块：ZIP，SSL等



​	Web服务器相关功能：

+ 虚拟主机
+ Keepalive
+ 日志访问
+ 路径别名
+ URL Rewrite
+ 访问控制



##### 程序架构

Master + Worker 模式

+ Master
  + 一个master进程可以生成一个或多个worker进程
  + 作用：加载配置文件，管理worker进程，平滑升级 等
+ Worker
  + 提供服务：http，http代理，FastCGI代理等





