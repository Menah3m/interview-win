### Ansible

-----



##### 功能

+ Configuration
+ Deployment
+ Ad-hoc Tasks
+ Multi-Tier Orchestration



##### 情境

> 每台主机在集群中的定位不同，在其上面安装的服务也不尽相同
>
> 功能对应着角色，每台主机可能身兼多种功能，每种角色也可能分配到不同的主机上
>
> 定义好每台主机的目标状态



##### 运维工具的分类

+ agent ：基于专用的agent程序完成管理功能，puppet，func，zabbix
+ agentless ：基于SSH服务完成管理，如ansible，fabric



##### 架构

+ ansible core
+ modules：
  + core modules
  + custom modules
+ host inventory
  + host files
  + CMDB
+ playbooks：
  + hosts
  + roles
+ connection plugins



##### 特性

+ 模块化
+ 基于Python研发，由paramiko，PyYAML和jinja2三个核心库实现
+ 部署简单：agentless
+ 支持自定义模块，使用任意编程语言
+ 强大的playbook功能
+ 幂等性



##### 安装和程序环境

+ 程序
  + ansible
  + ansible-doc
  + ansible-playbook
+ 配置文件
  + /etc/ansible/ansible.cfg
+ 主机清单
  + /etc/ansible/hosts
+ 插件目录
  + /usr/share/ansible_plugins



##### 基本使用

+ ansible命令

+ 配置host inventory

+ 模块

  + 获取模块列表： ansible-doc -l

  + 获取模块帮助       ansible-doc -s  module-name

  + 常用模块

    + ping 探测目标主机是否存活
    + command  在远程主机上执行命令
    + shell  在远程主机上调用shell解释器执行命令，支持shell的高级功能，比如管道

    注意：shell和command模块的参数直接为命令本身，其他模块的参数则为“key=value“

    + copy     1.复制文件    -a ” dest=   src= ”            2.   给定内容生成文件   -a " content=' '  dest=   "

    + file        1.创建目录   -a “path=  state=directory"   2.创建连接文件  -a "path= src= state=link "

      ​			 3.删除文件  -a "path= state=absent“
      
    + cron       定时任务       -a  "minute=   hour= day= month=   weekday=  job=   name=  state="

    + yum     程序包管理  -a “name= ”

    + service   控制服务状态    -a “name=   state= {started|stoppe|restarted}    ”

    + user

    + group

    + setup      

##### 





