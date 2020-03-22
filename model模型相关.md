

##### 运维开发中常用的model模型字段

+ CharField   字符串字段

+ IntegerField   整数型字段

+ DateTimeField  时间日期字段

+ ForeignKey  定义多对一关系




##### 模型迁移顺序

+ 定义模型
+ makemigrations   作用：生成迁移文件
+ migrate                   作用：根据迁移文件执行对应的SQL语句操作
+ 保存数据到数据库