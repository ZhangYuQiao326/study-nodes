# 提要

【MySQL上篇：基础篇】
【第1子篇：数据库概述与MySQL安装篇】
p01-p11
学习建议：零基础同学必看，涉及理解和Windows系统下MySQL安装

【第2子篇：SQL之SELECT使用篇】
p12-p48
学习建议：学习SQL的重点，必须重点掌握，建议课后练习多写

【第3子篇：SQL之DDL、DML、DCL使用篇】
p49-p73
学习建议：学习SQL的重点，难度较SELECT低，练习写写就能掌握

【第4子篇：其它数据库对象篇】
p74-p93
学习建议：对于希望早点学完MySQL基础，开始后续内容的同学，这个子篇可以略过。
在工作中，根据公司需要进行学习即可。

【第5子篇：MySQL8新特性篇】
p94-p95
学习建议：对于希望早点学完MySQL基础，开始后续内容的同学，这个子篇可以略过。
在工作中，根据公司需要进行学习即可。

【MySQL下篇：高级篇】
【第1子篇：MySQL架构篇】
p96-p114
学习建议：涉及Linux平台安装及一些基本问题，基础不牢固同学需要学习

【第2子篇：索引及调优篇】
p115-p160
学习建议：面试和开发的重点，也是重灾区，需要全面细致的学习和掌握

【第3子篇：事务篇】
p161-p186
学习建议：面试和开发的重点，需要全面细致的学习和掌握

【第4子篇：日志与备份篇】
p187-p199
学习建议：根据实际开发需要，进行相应内容的学习

祝你早日学成，成为MySQL大牛！

<font color='orange'>我是一个小可爱</font>







## 1 cmd启动/关闭服务/登录账号

```mysql
# 启动/关闭服务
net start mysql80
net stop mysql80
```

```mys
mysql -h 主机名 -P 端口号 -u 用户名 -p 密码
mysql -h localhost -P 3306 -u root -p
123456
```

注意： 

（1）-p与密码之间不能有空格，其他参数名与参数值之间可以有空格也可以没有空格。

（2）密码建议在下一行输入，保证安全

（3）客户端和服务器在同一台机器上，所以输入localhost或者IP地址127.0.0.1。同时，因   

​    为是连接本 机： -hlocalhost就可以省略，如果端口号没有修改：-P3306也可以省略

## 2 命令行导入数据库

```sql
souce D:/mysql.sql
```

## 3 创建/查看数据库/表格

```sql
show databases;
create database db;
use db;

show tables;
create table mytb(id int,name varchar(15));

select(distinct) [字段] as [新字段]                  -----1

from mytb1 as tb1 join mytb2 as tb2 on [条件]		  -----2
where [过滤、不聚合函数]
group by [字段]
having [过滤，有聚合函数]

order by [字段]                                     -----3
limit[条数]

```

* 执行顺序 2 -> 1-> 3
* 在select as中的别名，无法在from中使用，可以在order中使用
* 统一在from中起别名

## 4 聚合函数

```sql
max()  min()  avg()  count()  
```

select后面跟聚合函数字段的情况：

1、聚合函数操作的字段

```sql
select count(num) from table;
```

2、被分组的字段+聚合函数操作的字段

```sql
select name,count(num) from table group by name;
```

