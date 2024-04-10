// 2024-4-8 重新学习mysql

![image-20240408120409737](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240408120409737.png)



[参考资料](E:\typora索引文件\sql\mysql数据库课件.pdf)



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
drop database db;
use db;

show tables;
//(名称、类型、约束)
create table user(id int unsigned primary key not null auto_increment,
				  name varchar(50) not null,
				  age tinyint not null,
				  sex enum('M','W') not null)
				  engine=INNODB default charset=utf8;
drop table user;
// 查看表头结构
desc user;
// 查看创建表的sql语句,\G结尾
show create table user \G

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

### 增删改

```mysql
# auto_increment自动增长的键不用用户输入，自行增长
insert into user(name,age,sex) values('张三',12,'M');


delete from user where age between 20 and 22;
delete from user; // 清空表

update user set age=23 where name='zhang san';
update user set age=age+1 where id=3;

```

### 3.1 单表查询

#### 1 去重

```mysql
select distinct name from user;
```

#### 2 limit

```mysql
select name from user limit 3;   # 取前3条  （0，3）0开始，取3条
select name from user limit 1,4;  # 从1开始，取4条
```

* 在没有索引的情况下，limit可以优化查询效率
* 没有索引，使用where过滤，需要搜寻整张表
* 没有索引，使用where过滤，加上limit 1，搜索到第一条满足的记录就会停止

分页：

```mysql
# 每页20条
select * from user limit (page - 1)*20, 20;
# 问题： 偏移量的部分需要全部搜寻，导致越往后的页加载越慢
# 解决： 通过where+索引列进行过滤
select * from user where id > '前一页最后一条数据id' limit 20;
```

内联: 无法使用where提高效率，使用内联

```mysql
select * from user a
join (select id from user) b
on a.id = b.id;
```



#### 3 order by

```mysql
select name from user order by age asc; #默认升序
select name from user order by age desc;
```

* orderby的效率和 select的数据 和 排序基于的数据有关
* `select key from user order by key`, 是通过索引排序，效率高
* 其他情况，是通过外文件排序，效率低 

#### 4 group by

```mysql
select age from user group by age;
```

* group by 会默认排序，若是按照非索引键排序，会使用外排序`using filesort`效率低
* 通过索引键分组，会按照索引排序`using index`，效率高

### 3.2 多表连接查询

#### 1 内联查询

* 表重命名
* 流程：通过小表来查大表，小表全部扫描（索引没用），然后去大表搜寻

```mysql
select a.uid, a.name, b.cid, b.course from stu a 
join scores b on a.uid = b.uid
```

![image-20240409161922330](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240409161922330.png)

```mysql
select a.uid, a.name, b.cid, b.course from stu a 
join scores b on a.uid = b.uid
where a.uid = 2;
```

* 流程：当有where时，先执行where，得到最终的大小表，遍历小表所有，用小表搜寻大表

![image-20240409162114370](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240409162114370.png)

* inline join可以优化limit的查询效率

#### 2 左连接、右连接查询

* 输出左侧左右的数据，若是没有在右表中出现，则右表字段设为NULL

* 左连接和右连接 和 内联的不同点： 内联的过滤条件可以放在where 和 on 后，一样

  ​															左右连接的过滤必须放在on后用and连接，where之后只判断为null的情况

  ```mysql
  select a.* from student a left join exame b on a.uid = b.uid and b.cid = 3 where b.cid is NULL;
  ```

  

### 3.3 聚合函数

* 聚合函数一般结合分组

```sql
max()  min()  avg()  sum() count()  
```

select后面跟聚合函数字段的情况：

1、聚合函数操作的字段

```sql
select count(num) from table group by name;
```

2、被分组的字段+聚合函数操作的字段

```sql
select name,count(num) from table group by name;
```



## 4 数据类型、约束、范式

### 六大约束

* 创建表的时候设置字段的属性

* 外键约束基本不用（多张表关联）

  ![image-20240408161112747](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240408161112747.png)



![image-20240408161159307](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240408161159307.png)

在 MySQL 中创建表时设置主键的作用主要有以下几点：

1. **唯一性保证**：主键的值必须是唯一的，这意味着两行不能有相同的主键值。这个特性帮助保持数据的完整性，确保每一行都可以被唯一识别。

2. **索引**：主键自动成为表的一个索引。这使得基于主键的查询非常快速，因为数据库可以利用索引直接定位到数据的存储位置，而无需扫描整个表。

3. **关系完整性**：在关系型数据库中，表之间通过主键和外键建立关系。外键在一个表中指向另一个表的主键，这种机制支持将数据的完整性约束从一个表扩展到多个表，确保了数据库中数据的一致性和准确性。

4. **自增特性**：对于自增主键（通常是整数类型），数据库管理系统会在新行添加到表中时自动为该行生成一个唯一的主键值。这简化了新数据插入操作，因为用户不需要手动创建一个唯一的主键值。

```mysql
// 设置单个主键
CREATE TABLE Students (
    StudentID INT NOT NULL,
    Name VARCHAR(100),
    Age INT,
    PRIMARY KEY (StudentID)
);
// 设置联合主键
CREATE TABLE Enrollments (
    StudentID INT NOT NULL,
    CourseID INT NOT NULL,
    EnrollmentDate DATE,
    PRIMARY KEY (StudentID, CourseID)
);

```



### 四大范式

* 设计表时所需要遵循的规则

  1. **每一列保持原子特性** （满足关系型数据库）

     | id   | addr     |
     | ---- | -------- |
     | 11   | 山西运城 |

     修改：addr可以再拆分

     | id   | 省份 | 城市 |
     | ---- | ---- | ---- |
     | 11   | 山西 | 运城 |

  2. **一张表内的所有属性要完全依赖于主键**(均与主键有关系)

     id 与 课程联合主键

     | id   | 性别 | 课程 | 学分 |
     | ---- | ---- | ---- | ---- |
     | 1    | 男   | os   | 2    |

     修改：性别 与 课程无关、学分与id无关

     | id   | 性别 |
     | ---- | ---- |
     | 1    | 男   |

     | 课程 | 学分 |
     | ---- | ---- |
     | os   | 2    |

     | id   | 课程 |
     | ---- | ---- |
     | 1    | os   |

  3. **属性之间不能相互依赖**

  4. BC范式：**每一个表只有一个候选键**：每一个表中只有一个属性的值完全不同，其余所有的属性都有重复的

  5. **消除表中的多余依赖**

     | id   | skill      |
     | ---- | ---------- |
     | 1    | c++ golang |
     | 2    | c++ java   |

     修改：

     | 1    | c++    |
     | ---- | ------ |
     | 1    | golang |
     | 2    | c++    |
     | 2    | java   |

## 5 存储引擎、索引

MySQL的存储引擎是一种**数据表存储和处理机制的实现**。不同的存储引擎提供不同的数据表操作功能和性能优化，选择适合的存储引擎可以对数据库的性能和行为产生显著影响。

**MyISAM：**

不支持事务、也不支持外键，索引采用非聚集索引，其优势是访问的速度快，对事务完整性没 有要求，以 SELECT、INSERT 为主的应用基本上都可以使用这个存储引擎来创建表。MyISAM的表在磁 盘上存储成 3 个文件，其文件名都和表名相同，扩展名分别是：

| 后缀名 | 存储类型        |
| ------ | --------------- |
| .frm   | 存储表定义      |
| .MYD   | mydata 存储数据 |
| .MYI   | 存储索引        |

**Innodb:**存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全，支持自动增长列，外键等功能， 索引采用聚集索引，索引和数据存储在同一个文件，所以InnoDB的表在磁盘上有两个文件，其文件名 都和表名相同

| 后缀名 | 存储类型       |
| ------ | -------------- |
| .frm   | 存储表定义     |
| .ibd   | 存储数据和索引 |

**具体的引擎对比**

| 存储引擎 | 事务   | B-树索引 | 哈希索引 | 外键   | 事务安全 | 行级锁定 | 数据缓存 |
| -------- | ------ | -------- | -------- | ------ | -------- | -------- | -------- |
| MyISAM   | 不支持 | 支持     | 支持     | 不支持 | 不支持   | 支持     | 不支持   |
| InnoDB   | 支持   | 支持     | 不支持   | 支持   | 支持     | 支持     | 支持     |
| Memory   | 不支持 | 支持     | 支持     | 不支持 | 不支持   | 不支持   | 支持     |

锁机制：表示数据库在并发请求访问的时候，多个事务在操作时，并发操作的粒度。

 B-树索引和哈希索引：主要是加速SQL的查询速度。

外键：子表的字段依赖父表的主键，设置两张表的依赖关系。 

事务：多个SQL语句，保证它们共同执行的原子操作，要么成功，要么失败，不能只成功一部分，失败 需要回滚事务。 

索引缓存和数据缓存：和MySQL Server的查询缓存相关，在没有对数据和索引做修改之前，重复查询 可以不用进行磁盘I/O（数据库的性能提升，目的是为了减少磁盘I/O操作来提升数据库访问效率），读 取上一次内存中查询的缓存就可以了。



### 5.1 Innodb

### 5.2 索引

#### 1 优缺点

优： 当数据量非常大时，遍历查询耗时，使用索引加速查询

缺：索引也需要生成索引文件，使用索引需要访问索引文件，涉及io操作，因此索引数量需要合适



#### 2 分类

物理：聚集索引、非聚集索引

逻辑：一级索引：单列索引（主键索引primary kry、唯一索引unique）、联合索引

​			二级索引：

| 一级索引 | 分类                                                         |
| -------- | ------------------------------------------------------------ |
| 单列索引 | 主键索引primary key、唯一性索引unique，会自动创建索引        |
| 联合索引 | primary key(id, name)                                        |
| 全文索引 | 用于搜索字符串文本，不常用，全文搜寻使用搜索引擎（workflow） |

| 二级索引 | 分类                   |
| -------- | ---------------------- |
| 普通索引 | 可以给任何字段创建索引 |

#### 3 使用

* 创建二级索引

  ```mysql
  CREATE TABLE index1(id INT,
  name VARCHAR(20),
  sex ENUM('male', 'female'),
  INDEX(id,name)); // 创建索引
  
  # 在已经存在的表上创建索引
  create index 'nameidx' on student('name');
  
  # 删除索引
  drop index 'nameindx' on student;
  ```

#### 4 总结

1. 为经常作为where条件过滤的字段添加索引
2. 字符串列创建索引时，尽量规定索引的长度，而不能让索引的长度key_len过长
3. 当索引字段涉及类型强转，mysql的函数调用、表达式计算等，索引就失效










# n 面试题

> **1 mysql的服务器模型采用select + 线程池模型，为什么不适用epoll+线程池？**
>
> 考虑到mysql不仅向内存存储数据，还要向磁盘存储，考虑到磁盘IO

> 2 **说一下范式数据库**
>
> 作用是规范数据库字段的设计
>
> 最主要的优点就是**减少数据冗余**，其次：2 消除异常（插入、更新、删除异常）3 使数据组织更和谐
>
> 范式1 每一列保持原子属性，这是保持关系型数据库的基本要求
>
> 范式2 所有的属性都要依赖于主键
>
> 范式3 属性之间不能相互依赖
>
> 范式4 消除表中的多余依赖
>
> BC范式：每一个表只有一个候选键



> **3 limit分页如何提高效率？**
>
> 优化where效率：在where过滤非索引列时候，会遍历整张表，加上limit n后，找到满足的n条数据后，不在搜寻后面的数据
>
> * limit的效率与 select 的数据量  和 偏移量有关
>
>   ```mysql
>   select * from user limit 1,10;   # select量影响效率
>   select id from user limit 100000,10; # 偏移量影响效率
>   ```
>
> 优化limit效率：使用limit分页时候，为了保证不造成“越往后的页，加载越慢”，通长用where过滤索引列，来避免偏移部分的全部遍历
>
> ```mysql
> select * from user where id > 1000000 limit 10;
> ```
>
> inlint join优化查询了过大的效率
>
> ```mysql
> select * from user a 
> join (select id from user limit 10000, 10) b  # 查找索引效率常数级
> on a.id = b.id; # 小表查大表，通过索引联合，效率高
> ```
>
> 

> **4 设置主键的作用是什么？**
>
> 1. **唯一性保证**：主键的值必须是唯一的，这意味着两行不能有相同的主键值。这个特性帮助保持数据的完整性，确保每一行都可以被唯一识别。
>
> 2. **索引**：主键自动成为表的一个索引。这使得基于主键的查询非常快速，因为数据库可以利用索引直接定位到数据的存储位置，而无需扫描整个表。
>
> 3. **关系完整性**：在关系型数据库中，表之间通过主键和外键建立关系。外键在一个表中指向另一个表的主键，这种机制支持将数据的完整性约束从一个表扩展到多个表，确保了数据库中数据的一致性和准确性。
>
> 4. **自增特性**：对于自增主键（通常是整数类型），数据库管理系统会在新行添加到表中时自动为该行生成一个唯一的主键值。这简化了新数据插入操作，因为用户不需要手动创建一个唯一的主键值。
>
> 总之，主键是确保数据完整性和优化数据检索性能的重要机制。通过强制每行数据的唯一性，以及作为快速检索数据的索引
