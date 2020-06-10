#### 目标

+ 采集信息
  + 主机信息
  + CPU信息
  + 内存信息
  + 硬盘信息
  + 网卡信息

+ 实现资产变更的自动汇报

+ 代码发布系统
+ 监控
+ 服务器统计
  + 实现自动化采集

#### 方案选择

共有三种方案可供选择：

+ agent
  + 在目标机器上安装agent脚本，定期任务执行后通过requests模块将得到的信息通过API存入数据库，再通过前端页面进行展示
  + 优点：速度快，适合数量多的场景
  + 缺点：需要事先安装agent
+ ssh类
  + 创建若干个中控机，通过ssh协议（paramiko模块实现，或者使用现成的工具如Ansible）等连接目标主机，执行命令后将得到的信息通过API存入数据库，再通过前端页面显示
  + 缺点：速度慢，适合数量少的场景，且需要若干台中控机，需要服务器成本
  + 优点：不需要安装agent，不需要考虑脚本的更新
+ salt类
  + salt-master和salt-minion
  + 中控机扮演master角色，目标主机扮演minion角色。利用salt-stack现成的工具来收集信息
  + 适合正在使用salt-stack或想要使用salt-stack的场景

#### 架构分工

+ 资产采集（1-2人）
+ 资产数据处理，入库（1人）
+ Web端数据的展示（1人）

#### 代码实现

##### 资产采集

+ 设计目录结构
  + bin   可执行文件
  + conf  配置文件
  + lib     库文件目录
  + src    源码目录
  + test   测试文件目录
  + log    日志文件（不要在代码中写日志文件的目录：防止容量越来越大导致整个项目大小过重）
+ 高级配置文件实现
  + 参考Django的配置源码
  + 目标：通过一个类对象，获取自定义配置和高级配置
  + 核心代码：
    + dir()
    + getattr()
    + setattr()

+ 采集信息实现

  + 传统的做法：根据mode来进行条件判断，分别实行不同的执行过程（面向过程）

    + 存在的问题：1.面向过程 2.代码过于冗余，不利于复写 3.优化比较麻烦

  + 高内聚低耦合原则：

    + 这是一种设计思想
    + 一个类或者一个函数中，不能有一行代码和此类或者此函数所阐述的功能不一致

  + 写代码的原则（review的要点）：

    + 功能是否能实现
    + 代码的整洁度
      + 变量、方法和类的命名风格要一致
        + 大驼峰 GetUserInfo
        + 小驼峰 getUserInfo
        + 下划线 get_user_info
      + 变量名要有意义
      + 函数体内部的代码不能超过一定的行数

  + 改进方法：

    + 将采集的模块代码封装成一个插件文件

      + disk： disk.py -->  Disk():  ----> process()
      + cpu:     cpu.py -->   Cpu():  ---->process()

    + 设置一个字典来保存各个类，用来决定初始化时是否导入（可插拔式）

      + 如果不想使用某个功能，可以将字典中的该项注释掉（类似Django中）

      + 配置文件：

        + PLUGIN_DICT = {

            'basic': 'src.plugins.basic.Basic',

            'cpu': 'src.plugins.cpu.Cpu',

            'disk': 'src.plugins.disk.Disk',

            'memory': 'src.plugins.memory.Memory',

            'nic': 'src.plugins.nic.Nic',

          }

      + 插件：

        + src:
          + plugins
            + \__init__.py:实现可插拔式功能的关键
              + execute()
            + basic.py --> Basic -->process()
            + cpu.py-->Cpu-->process()
            + memory-->Memory-->process()
            + disk-->Disk-->process()
            + nic-->Nic-->process()

