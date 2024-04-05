# 一 HADOOP

```
// node1 node2 node3 
账号密码 root 123456
// datagrip连接node1上的hive数据仓库
账号 root 无密码
```



## 1.1 完全分布式运行模式

### 1.1.1 sep安全拷贝文件

```
sep -r /opt/module/jdk.1.2 zhang@hadoop103:/opt/module
# 可以从任意一台服务器操控两台另外的服务器进行拷贝
# ./ 代表当前路径简写
# *  代表该文件下的所有子文件
```

### 1.1.2 rsync 远程同步工具

```
rsync -av /opt/module/jdk.1.2 zhang@hadoop103:/opt/module
# 第一次同步相当于复制
# 参数和sep一致
# 同步只是更新被更新过的内容
rsync -av /opt/module  root@hadoop103:/opt/

```

### 1.1.3 xysnc 集群分发脚本

```
xysnc /home/zhang/bin/
#将一个服务器的某个目录同步到所有服务器的相同目录下
#本脚本设置的是hadoop102 103 104
#设置参考上课笔记
```

### 1.1.4 ssh免密登录

```
ssh hadoop102
exit
# 免密配置参考笔记
# 每个用户都需要单独配置 root、zh
```

## 1.2 HDFS操作（hadoop fs -命令）

### 1.2.1 启动准备

```
sbin/start-dfs.sh
sbin/start-yarn.sh
```

### 1.2.2 上传

```
# linux本地剪切上传
hadoop fs -moveFromLocal [linux路径] [hdfs上传路径]

# linux本地复制上传
hadoop fs -copyFromLocal [linux路径] [hdfs上传路径]

# 生产环境常用（等同复制上传）
hadoop fs -put [linux路径] [hdfs上传路径]

# linux本地追加上传
hadoop fs -appendToFile [linux路径] [hdfs上传路径]
```

### 1.2.3 下载

```
#从hdfs上复制下载
hadoop fs -copyToLocal [hdfs路径][linux路径]

# 工作中常用
hadoop fs -get [hdfs路径][linux路径]
```

### 1.2.4 其他操作

```
# 创建目录
hadoop fs -mkdir /xxx
# 查看目录/逐层进入目录
hadoop fs -ls /xxx
# 查看文件
hadoop fs -cat /xxx
hadoop fs -chgrp [新组名][文件路径]
#修改文件权限
hadoop fs -chmod

hadoop fs -chown  [新拥有者用户名][文件路径]
hadoop fs -cp [文件路径][复制后的文件路径]
hadoop fs -mv [文件路径][复制后的文件路径]
# 显示文件末尾1kb的东西
hadoop fs -tail [文件路径]
hadoop fs -rm

# 统计文件总大小
hadoop fs -du -r -h
#统计文件单个文件大小
hadoop fs -du -h

hadoop fs -setrep
```

### 1.2.5 windows本地从hdfs中读文件

```
// spark为例
sc.textFile("hdfs://node1:8020/xx/xx")
```



## 1.3 启动hadoop（HDFS+yarn）

![image-20230210195313912](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230210195313912.png)





![image-20230210195334821](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230210195334821.png)

![image-20230210195355580](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230210195355580.png)

![image-20230210195406575](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230210195406575.png)

## 1.4 启动hive服务（metastore服务）

```
配置的mysql服务器在node1上
>mysql -u root -p
hadoop
```



![image-20230210200635814](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230210200635814.png)

```scala
//启动metastore服务
nohup /export/server/apache-hive-3.1.2-bin/bin/hive --service metastore &

//启动server2服务
nohup /export/server/apache-hive-3.1.2-bin/bin/hive --service hiveserver2 &
```

```scala
//启动第一代客户端
/export/server/apache-hive-3.1.2-bin/bin/hive
hive>

//启动第二代客户端
/export/server/apache-hive-3.1.2-bin/bin/beeline
beeline> ! connect jdbc:hive2://node1:10000


```

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230210200804219.png" alt="image-20230210200804219" style="zoom: 67%;" />



----



# 二 HIVE SQL

hive中所使用的数据库存放在hdfs://user/hive/warehouse中

## 2.1 基础语法

* 删除库

```sql
drop database name cascade
```

```sql
// 显示表的元数据信息
desc formatted name;
```

* 建表

```sql
create table king_honor(
    id int comment "序号",
    name string comment "英雄名称",
    hp_max int comment "最大生命",
    mp_max int comment "最大法力",
)
row format delimited
fields terminated by "\t";
// 按制表符分隔数据
```

* 加载数据到表

```sql
//1 直接hdfs页面上传windows文件

//2 加载hive服务器所在服务端的本地文件
load data local inpath '/xx/xx' into table database_name.table_name;

//3 加载hdfs中的文件
load data inpath '/xx/xx' into table database_name.table_name;
```

* select操作

```sql
// 去重
select distinct name from students;
// 筛选
select distinct name from students where age = 10;
select distinct name from students where age = 10 and scord = 100;
select distinct name from students where age between 10 and 20;
select distinct name from students where age in (10,20,30);
```

* 重命名

```sql
// 查询结果重命名 as
select distinct name as myname from students;

// 表重命名  直接跟后面
select distinct name as myname from students stu;
```



## 2.2 聚合函数

* 最终只返回一个数据的函数，不能放在where后面，放在select后
* 常见聚合：avg()，max()， min()， sum()， count()

```sql
--统计美国总共有多少个县county
select count(county) from t_usa_covid19;
--统计美国加州有多少个县
select count(county) from t_usa_covid19 where state = "California";
--统计德州总死亡病例数
select sum(deaths) from t_usa_covid19 where state = "Texas";
--统计出美国最高确诊病例数是哪个县
select max(cases) from t_usa_covid19;


-- 去重
select count(distinct county) from t_usa_covid19 where state = "California";
```

* 保留小数

```sql
select count(avg("age"),2) from list
```



## 2.3 分组

* 有分组必有聚合，有聚合未必有分组

* 分析要求：“每一个xx的值”，‘’'所有xx的值'
* 通常group by和聚合函数搭配使用
* 语法限制：出现在分组语法中，select后的字段，必须是 1. 按此字段分组   2. 被聚合函数引用的字段
* 其余字段搭配聚合函数使用

```sql
-- 根据state州进行分组 统计每个州有多少个县county,每个县的死亡病例数
select state,count(county),sum(deaths) from t_usa_covid19 where count_date = "2021-01-28" group by state;
```

## 2.4 过滤having

```sql
-- where过滤后不能跟聚合函数
-- having过滤后可以跟聚合函数

-- 统计2021-01-28死亡病例数大于10000的州
select state,sum(deaths) as cnts from t_usa_covid19 where count_date = "2021-01-28" group by state having cnts> 10000;

```

## 2.5 排序

```sql
-- 默认order by 用asc从小到大升序
-- desc降序
```

```sql
--根据死亡病例数倒序排序 查询返回加州每个县的结果
select * from t_usa_covid19 where state = "California" order by cases desc;
```

## 2.6 limit限制

```sql
-- 用法1 ，1个参数
-- 返回前5条数据
select * from usa_cov19 order by cases limit 5;
```

```sql
-- 用法2 ，2个参数，偏移量是a到b，即返回，第a+1~b+1条数据
select * from usa_cov19 order by cases limit 2，4;
-- 返回第3，4，5三条数据
```

## 2.7 join连接

// 注意写明是查询哪张表的变量

* 内连接 join/inner join

  <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230211172226270.png" alt="image-20230211172226270" style="zoom:33%;" />

```sql
// 显示连接
select e.id, e.name,e_a.city, e_a.street
from employee e join employee_address e_a
on e.id =e_a.id;

// 隐式连接
select e.id,e.name,e_a.city,e_a.street
from employee e , employee_address e_a
where e.id =e_a.id
```

* 左连接 left join/ left outer join

  <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230211172248106.png" alt="image-20230211172248106" style="zoom:33%;" />

```sql
select e.id,e.name, e_conn.phno, e_conn.email
from employee e left join employee_connection e_conn
on e.id =e_conn.id;
```

## 2.8 函数

* 内置函数，用户定义函数

### 2.8.1 字符串

```sql
select length("itcast");

select reverse("itcast");

select concat("angela","baby");

--带分隔符字符串连接函数：concat_ws(separator, [string | array(string)]+)
select concat_ws('.', 'www', array('itcast', 'cn'));

--字符串截取函数：substr(str, pos[, len]) 或者 substring(str, pos[, len])
select substr("angelababy",-2); --pos是从1开始的索引，如果为负数则倒着数
select substr("angelababy",2,2);

--分割字符串函数: split(str, regex)
select split('apache hive', ' ');
```

### 2.8.2 日期

```sql
--获取当前日期: current_date
select current_date();

--获取当前UNIX时间戳函数: unix_timestamp
select unix_timestamp();

--日期转UNIX时间戳函数: unix_timestamp
select unix_timestamp("2011-12-07 13:01:03");

--UNIX时间戳转日期函数: from_unixtime
select from_unixtime(1618238391);
```

```sql
--日期比较函数: datediff 日期格式要求'yyyy-MM-dd HH:mm:ss' or 'yyyy-MM-dd'
select datediff('2012-12-08','2012-05-09');

--日期增加函数: date_add
select date_add('2012-02-28',10);

--日期减少函数: date_sub
select date_sub('2012-01-1',10);
```

###  2.8.3 数字

```sql
--取整函数: round 返回double类型的整数值部分 （遵循四舍五入）
select round(3.1415926);

--指定精度取整函数: round(double a, int d) 返回指定精度d的double类型
select round(3.1415926,4);

--取随机数函数: rand 每次执行都不一样 返回一个0到1范围内的随机数
select rand();

--指定种子取随机数函数: rand(int seed) 得到一个稳定的随机数序列
select rand(3);
```

### 2.8.4 条件

```sql
--if条件判断: if(boolean testCondition, T valueTrue, T valueFalseOrNull)
select if(1=2,100,200);
select if(sex ='男','M','W') from student limit 3;

--空值转换函数: nvl(T value, T default_value)
select nvl("allen","itcast");
select nvl(null,"itcast");

--条件转换函数: CASE a WHEN b THEN c [WHEN d THEN e]* [ELSE f] END
select case 100 when 50 then 'tom' when 100 then 'mary' else 'tim' end;
select case sex when '男' then 'male' else 'female' end from student limit 3;
```

## 2.9 执行顺序

```sql
select distinct xx
from xx
where xx
group by xx
having xx
order by xx
limit xx

```

## 2.10 CTAS语法

* 将查询结果存到新建的表中

```sql
creat table if not exists name1
as
select sex 
from
students;
```



## 2.11 momo聊天记录分析

```sql
-- 1.建库建表、加载数据
create table momo_analyse (
    msg_time string comment "消息发送时间",
    sender_name string comment "发送人昵名",
    sender_account string comment "发送人账号",
    sender_sex string comment "发送人性别",
    sender_ip string comment "发送人ip",
    sender_os string comment "发送人操作系统",
    sender_phonetype string comment "发送人手机型号",
    sender_network string comment "发送人网络类型",
    sender_gps string comment "发送人GPS定位",
    receiver_name string comment "接收人姓名",
    receiver_ip string comment "接收人ip",
    receiver_account string comment "接收人账号",
    receiver_os string comment "接收人操作系统",
    receiver_phonetype string comment "接收人手机型号",
    receiver_network string comment "接收人网络",
    receiver_gps string comment "接收人gps",
    receiver_sex string comment "接收人性别",
    msg_type string comment "消息类型",
    distance string comment "双方距离",
    message string comment "消息内容"
)
row format delimited
fields terminated by "\t";

-- 1.1 检验是否创建成功、以及数据量
select * from momo_analyse limit 5;
select count(sender_account) from momo_analyse;

-- 2. ETL清洗数据、另存新表
-- 2.1 需求1：对字段为空的不合法数据进行过滤，where过滤
-- 2.2 需求2：通过时间字段构建天和小时字段，substr()截取
-- 2.3 需求3：从GPS经纬度中提取经度和纬度，split()分割
-- 2.4 需求4：将ETL后的结果保存到一张新的Hive表中,CTAS语法创建

create table momo_analyse_etl as
    select *,
           substr(msg_time,1,10) as dayinfo,
           substr(msg_time,12,2) as hourinfo,
           split(sender_gps,",")[0] as send_gps_j,
           split(sender_gps,",")[1] as send_gps_w
    from momo_analyse
    where length(sender_gps)>0;

-- 2.5 检验是否创建成功
select * from momo_analyse_etl limit 5;
select count(*) from momo_analyse_etl;

-- 3.业务分析
-- 3.1 统计今日消息总量
create table today_total_msg
    comment "今日消息总量"
    as
    select dayinfo,
           count(*) as total_msg
    from momo_analyse_etl
    group by dayinfo;

select * from today_total_msg ;

-- 3.2 统计每小时的消息量、发送和接收信息的用户数（去重）
create table hour_msg_sender_rever
comment "每小时的消息量、发送和接收信息的用户数"
as
    select dayinfo,
           hourinfo,
           count(*) as hour_msg,
           count(distinct sender_account) num_sender,
           count(distinct receiver_account) num_receiver

    from momo_analyse_etl
    group by dayinfo,hourinfo;

select * from hour_msg_sender_rever limit 5;

-- 3.3 统计今日各地区发送消息总量
create table today_gps_msg
comment "今日各地区发送消息总量"
as
    select dayinfo,
           sender_gps,
           count(*) as msg_total,
           cast(send_gps_j as double) as longitude,
           cast(send_gps_w as double) as latitude
    from momo_analyse_etl
    group by dayinfo, sender_gps,send_gps_j,send_gps_w;

select * from today_gps_msg limit 5;

-- 3.4 统计今日发送和接收用户的总数（去重）
create table today_num_sender_receiver
comment "今日发送和接收用户的人数"
as
    select dayinfo,
           count(distinct sender_account),
           count(distinct receiver_account)
    from momo_analyse_etl
    group by dayinfo;

select * from today_num_sender_receiver;

-- 3.5 统计每天发送消息条数最多的top10用户
create table top10_sender
comment "每日发送消息条数最多的top10用户"
as
    select dayinfo,
           sender_name,
           count(sender_name) as num_user_msg
    from momo_analyse_etl
    group by  dayinfo,sender_name
    ORDER BY num_user_msg DESC
    LIMIT 10;

select * from top10_sender;

-- 3.6 统计接收消息条数最多的Top10用户
create table top10_receIer
comment "每日接收消息条数最多的top10用户"
as
    select dayinfo,
           receiver_name,
           count(receiver_name) as num_user_msg
    from momo_analyse_etl
    group by  dayinfo,receiver_name
    ORDER BY num_user_msg DESC
    LIMIT 10;

select * from top10_receIer;

-- 3.7 统计发送人的手机型号分布
create table phonetype_sender
comment "发送人的手机型号分布"
as
    select dayinfo,
           sender_phonetype,
           count(*) as num_phone
    from momo_analyse_etl
    group by dayinfo ,sender_phonetype;

select * from phonetype_sender;

-- 3.8 统计发送人的操作系统分布
create table os_sender
comment "发送人的操作系统分布"
as
    select dayinfo,
           sender_os,
           count(*) as num_os
    from momo_analyse_etl
    group by dayinfo,sender_os;

select * from os_sender;

```



## 2.12datagrip快捷键

1. ctrl+q 快速查看表信息

--------------

# 三 Spark基础

共有三个spark客户端用来提交任务`spark/bin`下

standalone模式

```shell
bin/pyspark --master spark://node1:7077
# 通过--master选项来连接到 StandAlone集群
# 如果不写--master选项, 默认是local模式运行
```

```shell
bin/spark-shell --master spark://node1:7077
# 同样适用--master来连接到集群使用
```

```shell
bin/spark-submit --master spark://node1:7077 /export/server/spark/examples/src/main/python/pi.py 100
# 同样使用--master来指定将任务提交到集群运行
```





## 3.1 架构

### 3.1.1 Spark运行角色

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220161750539.png" alt="image-20230220161750539" style="zoom:50%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220161916480.png" alt="image-20230220161916480" style="zoom:50%;" />

### 3.1.2 代码分布式执行

```
// SparkContext是什么
SparkContext 是通往 Spark 集群的唯一入口，可以用来在 Spark 集群中创建 RDDs 、累加和广播变量( BroadcastVariables )。

SparkContext 也是整个 Spark 应用程序( Application )中至关重要的一个对象，可以说是整个 Application 运行调度的核心(不是指资源调度)。

// 作用
SparkContext 的核心作用是初始化 Spark 应用程序运行所需要的核心组件，包括高层调度器( DAGScheduler )、底层调度器( TaskScheduler )和调度器的通信终端( SchedulerBackend )，同时还会负责 Spark 程序向 Master 注册程序等。

只可以有一个 SparkContext 实例运行在一个 JWM 内存中，所以在创建新的 SparkContext 实例前，必须调用 stop 方法停止当前 JVM 唯一运行的 SparkContext 实例。

// 重要性
park 程序在运行时分为 Driver 和 Executor 两部分， Spark 程序编写是基于 SparkContext 的，具体包含。

Spark 编程的核心基础 RDD 是由 SparkContext 最初创建的(第一个 RDD 一定是由 SparkContext 创建的)
Spark 程序的调度优化也是基于 SparkContext ，首先进行调度优化。
Spark 程序的注册是通过 SparkContext 实例化时生产的对象来完成的(其实是 SchedulerBackend 来注册程序)。
Spark 程序在运行时要通过 ClusterManager 获取具体的计算资源，计算资源获取也是通过 SparkContext 产生的对象来申请的(其实是 SchedulerBackend 来获取计算资源的)。
SparkContext 崩溃或者结束的时候，整个 Spark 程序也结束。
```



<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230222095325814.png" alt="image-20230222095325814" style="zoom: 67%;" />

```
大部分提交的代码都是由driver开始，到driver结束，中间由executer执行。

Driver执行非任务处理部分（非RDD）代码
Executer执行任务处理（RDD）代码
```



### 3.1.3 python on spark的执行原理

![image-20230222101543230](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230222101543230.png)

* spark底层由scala编写，即运行在jvm之上
* Driver端： 将python转为jvm代码
* jvm Driver和jvm Executer通讯
* jvm Executer通过中转站转为python Executer

![image-20230222101837232](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230222101837232.png)







## 3.2 spark执行模式

### 3.2.1 local模式

在单个服务器运行

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    # 连接spark集群
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)
```

### 3.2.2 standalone模式

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220163312336.png" alt="image-20230220163312336" style="zoom: 50%;" />

![image-20230220163331391](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220163331391.png)

### 3.2.3 spark on yarn模式

* cluster集群模式--driver运行在容器内，和applicationMaster在一块

```shell
bin/spark-submit --master yarn --deploy-mode client|cluster /xxx/xxx/xxx.py 参数
```

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220180430884.png" alt="image-20230220180430884" style="zoom:50%;" />



<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220180445899.png" alt="image-20230220180445899" style="zoom:50%;" />

* client客户端模式 --driver运行在客户端进程内（3个spark客户端）

```shell
bin/pyspark --master yarn --deploy-mode client
# --deploy-mode 选项是指定部署模式, 默认是 客户端模式
# client就是客户端模式
# cluster就是集群模式
# --deploy-mode 仅可以用在YARN模式下
```

```shell
bin/spark-shell --master yarn --deploy-mode client
```

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220180530329.png" alt="image-20230220180530329" style="zoom:50%;" />![image-20230220180549799](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220180549799.png)

* 总结

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230220180706364.png" alt="image-20230220180706364" style="zoom:50%;" />

* Pycharm提交yarn执行

```py
# 1. 读取的文件必须存放在hdfs中
# 2. 在pycharm中声明yarn的环境变量
# 3. 若项目有多个依赖文件，需要一块上传提交到yarn
#    因为main和依赖文件存在于一台服务器上，main文件提交到spark on yarn上可以集群共享，但是依赖只在一个服务器上，不能被集群共享，所      以要上传依赖

from pyspark import SparkConf, SparkContext
import json

# 2.----------提交到YARN上执行时，需要在pycharm中声明yarn的环境变量
import os
os.environ["HADOOP_CONF_DIR"] = "/export/server/hadoop-3.3.0/etc/hadoop"

# 导入依赖包
from result_def import type_result

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("yarn")
    # 3---------给yarn上传依赖（在同一目录下可直接输入文件名，注意后缀）
    conf.set("spark.submit.pyFiles", "result_def.py")
    sc = SparkContext(conf=conf)

    # 1.-----------读取json文件为字符串
    file_rdd = sc.textFile("hdfs://node1:8020/input/data/order.txt", 3)
    # 分割为单个json字符串
    json_rdd = file_rdd.flatMap(lambda line: line.split('|'))
    # 所有的json字符串转为字典
    dict_rdd = json_rdd.map(lambda i: json.loads(i))
    # 筛选字典
    select_rdd = dict_rdd.filter(lambda i: i["areaName"] == "北京")
    # 输出结果样式
    res_rdd = select_rdd.map(type_result)
    # 去重
    dist_rdd = res_rdd.distinct()
    print(dist_rdd.collect())
```

* linux使用submit提交yarn执行

```shell
# 1. 读取的文件必须存放在hdfs中
# 2. 不需要在代码中设置环境变量
# 3. 不需要设置master形式，直接将local模式下的main文件和defs依赖文件上传到linux
# 4. 通过submit语法上传依赖执行spark
from pyspark import SparkConf, SparkContext
import json

# 导入依赖包
from result_def import type_result

if __name__ == '__main__':
    conf = SparkConf().setAppName("testOnYarn")
    sc = SparkContext(conf=conf)
    
    # 读取json文件为字符串
    file_rdd = sc.textFile("hdfs://node1:8020/input/data/order.txt", 3)
    # 分割为单个json字符串
    json_rdd = file_rdd.flatMap(lambda line: line.split('|'))
    # 所有的json字符串转为字典
    dict_rdd = json_rdd.map(lambda i: json.loads(i))
    # 筛选字典
    select_rdd = dict_rdd.filter(lambda i: i["areaName"] == "北京")
    # 输出结果样式
    res_rdd = select_rdd.map(type_result)
    # 去重
    dist_rdd = res_rdd.distinct()
    print(dist_rdd.collect())
    
# linux执行命令
submit---master---py-files(依赖文件)--执行主文件
 /export/server/spark/bin/spark-submit --master yarn --py-files /home/result_def.py /home/main.py 

```

![image-20230223125344387](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230223125344387.png)

```py
///export/server/spark/bin/spark-submit --master yarn --py-files /tmp/pycharm_project_882/03_jieba/defs.py --executor-memory 2g --executor-cores 1 --num-executors 3 /tmp/pycharm_project_882/03_jieba/test_onYarn.py
```

![image-20230225183105318](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225183105318.png)

## 3.3 linux操作

* 启动历史服务器

```
cd /export/server/spark

sbin/start-history-server.sh
// jps 中多出 HistoryServer
```

* 启动spark集群

```shell
# 在node1上 启动一个master 和全部worker
# jps中多出Master和Worker
sbin/start-all.sh
```
## 3.4 pycharm操作

### 3.4.1 conda配置pyspark环境

1. 环境：即，包括一个项目中所需要的解释器，下载的包等。可以提前在conda中配置好，然后直接在pycharm中使用该解释器，也可以在pycharm中创建项目时候新建一个环境（解释器）
2. 学习spark中创建了，windows本地的pyspark环境，linux中pyspark环境（使用时需要远程ssh连接）

### 3.4.2 pycharm中使用spark远程操作linux

* 建立连接

  ```py
  conf = SparkConf().setMaster("local[*]").setAppName("WordCountHelloWorld")
  sc = SparkContext(conf=conf)
  ```

* 读取hdfs上文件

  ```py
  file_rdd = sc.textFile("hdfs://node1:8020/worldCount.txt")
  ```

* 读取pycharm中的文件（相当于远程上传到linux的文件）

  ```
  在pycharm中创建的文件都会同步上传到linux中 /tmp/pycharm_projet123/  目录下，所以使用相对路径../xx 直接调用
  ```

  ```
  file_rdd = sc.textFile("../worldCount.txt")
  ```
  
* 注意：若spark执行local模式，文件可存于linux或这hdfs中

  ​            若执行yarn模式，则，文件必须存与hdfs中



-----------

# 四 Spark Core

## 4.1 RDD

### 4.1.1 定义

![image-20230222110728236](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230222110728236.png)

* RDD分区是RDD存储数据的最小单位

### 4.1.2 五大特性

* RDD是有分区的；即从HDFS中读取的数据，以分区的形式存在已经操作
* RDD的方法会作用在所有的分区上；即宏观上对整个文件进行操作，微观上是对所有的分区进行操作
* 所有的RDD是有联系的；即一个项目中RDD都是在前一个RDD的基础上操作得来的
* Key-Value型的RDD有分区器
* RDD的分区规划，会尽量靠近在数据所在的服务器；即在数据所在的服务器上创建分区加载其上的数据

![image-20230222150618152](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230222150618152.png)



### 4.1.3 RDD创建

三种方法

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    # 00.初始化创建SparkContext对象
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    # 01.调用paralelizeAPI创建并行化集合RDD（本地输入）
    rdd1 = sc.parallelize([1, 2, 3, 4, 5, 6], 3)
    # 通过getNumPartitions获得分区的数量
    print("分区数量：", rdd1.getNumPartitions())
    # 通过collect显示出RDD
    print(rdd1.collect())

    # 02. 调用textLines读取本地文件，linux文件，HDFS文件
    rdd2 = sc.textFile("../data/words.text", 7)
    print("分区数量：", rdd2.getNumPartitions())
    print(rdd2.collect())

    # 03. 调用wholeTextFile读取多个小文件
    rdd3 = sc.wholeTextFiles("../data/input/tiny_files")
    print(rdd3.collect())
    # 读取多个文件，返回一个包含多个kv元组的列表，调用mapAPI仅显示出value值
    print(rdd3.map(lambda x: x[1]).collect())
```

```py
// 显示结果
分区数量： 3

[1, 2, 3, 4, 5, 6]

分区数量： 8

['hello world', 'i love you']

[('file:/tmp/pycharm_project_882/data/input/tiny_files/1.txt', 'hello spark\r\nhello hadoop\r\nhello flink'), 
 ('file:/tmp/pycharm_project_882/data/input/tiny_files/2.txt', 'hello spark\r\nhello hadoop\r\nhello flink'), 
 ('file:/tmp/pycharm_project_882/data/input/tiny_files/3.txt', 'hello spark\r\nhello hadoop\r\nhello flink'), 
 ('file:/tmp/pycharm_project_882/data/input/tiny_files/5.txt', 'hello spark\r\nhello hadoop\r\nhello flink'),
 ('file:/tmp/pycharm_project_882/data/input/tiny_files/4.txt', 'hello spark\r\nhello hadoop\r\nhello flink')]

['hello spark\r\nhello hadoop\r\nhello flink', 'hello spark\r\nhello hadoop\r\nhello flink', 'hello spark\r\nhello hadoop\r\nhello flink', 'hello spark\r\nhello hadoop\r\nhello flink', 'hello spark\r\nhello hadoop\r\nhello flink']

进程已结束,退出代码0
```

### 4.1.4 RDD类型

* rdd均为数组形式（数字，字符串）

```
rdd1 = sc.paralelize([1,2,3])
rdd2 = sc.paralelize(['a','b','c'])
```

* 可以是数组嵌套数组

```py
rdd3 = sc.paralelize([[1,2],['a','b']])
```

* 可以是数组嵌套kv元组

```
rdd4 = sc.paralelize([('a',1),('b',3)])
```

### 4.1.5 读取对象转化

* 将字典读取到rdd中为字符串，需要再次转化为字典
* 通过json.loads()

```py
# 原文件纯json文件
txt/  {'a':1,'b':2,'c':3}  {'d':4, 'e':5}
# rdd加载
file = sc.textFile("../txt")
# 输出,字符串形式
['{'a':1,'b':2,'c':3}','{'d':4, 'e':5}']
# 转为字典
import json
dict_file = file.map(lambda str: json.loads(str))
#结果
[{'a':1,'b':2,'c':3}, {'d':4, 'e':5}]
```



## 4.2 Transformation算子

###  4.2.1 定义

* 方法/函数：本地对象的API

* 算子：分布式对象的API

* RDD算子分两种：Transfromation:转换算子---------返回值仍旧是RDD

  ​                               Action：行动算子---------返回值不是RDD，这类算子是整个流水线的开关

### 4.2.2 map

* 对每一个数据（第一层列表内的每一个元素）进行操作

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([1, 2, 3, 4], 3)
    rdd2 = rdd1.map(lambda x: x * 10)
    print(rdd2.collect())
    
// 结果
[10, 20, 30, 40]
```

* 对于嵌套列表（ x指代[1,2] ）

```py
rdd1 = sc.parallelize([[1, 2], [3, 4]], 3)
rdd2 = rdd1.map(lambda x: [x[0] * 10, x[1] * 10])

// 结果
[[10, 20], [30, 40]]
```

* 对于嵌套元组( x 指代  ('a', 2) )-------------------------mapValue算子代替

```py
rdd1 = sc.parallelize([('a', 2), ('b', 4)], 3)
rdd2 = rdd1.map(lambda x: (x[0], x[1] * 10))
print(rdd2.collect())

// 结果
[('a', 20), ('b', 40)]
```

### 4.2.3 flatmap

* 对map返回的rdd进行操作
* 并对结果进行解嵌套

```py
rdd3 = sc.parallelize(["hello me", "hello you", "love we"], 3)

# 一行数据在一个列表
rdd5 = rdd3.map(lambda line: line.split(" "))
print(rdd5.collect())

# 全部数据在一个列表
rdd4 = rdd3.flatMap(lambda line: line.split(" "))
print(rdd4.collect())

// 结果
[['hello', 'me'], ['hello', 'you'], ['love', 'we']]
['hello', 'me', 'hello', 'you', 'love', 'we']
```

* 仅解嵌套，不用操作

```py
rdd2 = rdd1.flatMap(lambda x:x)
```



### 4.2.4 reduceByKey

* 针对kv元组型RDD
* 自动按照key一致的分组（by key）
* 根据自定义的聚合函数，对组内value进行聚合操作（reduce）

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([('a', 1), ('b', 2), ('c', 3), ('a', 2), ('b', 4), ('c', 8)], 3)
    // 常用
    rdd2 = rdd1.reduceByKey(lambda a, b: a+b)
    print(rdd2.collect())
   
// 结果
[('b', 6), ('a', 3), ('c', 11)]
```

```py
// 将列表按照元素为key，value为1转为元组
rdd1 = sc.paralelise(['a','b','c'])
rdd2 = rdd1.map(lamebda x: (x , 1))
```

### 4.2.5 groupByKey

* 针对kv型RDD
* 仅按照key分组，将value放在一个list中
* 不对value聚合
* 注意和groupBy区别

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([('a', 1), ('b', 2), ('c', 3), ('a', 2), ('b', 4), ('c', 8)], 3)

    rdd2 = rdd1.groupByKey()
    print(rdd2.map(lambda x: (x[0], list(x[1]))).collect())
    
// 结果
('b', [2, 4]), ('a', [1, 2]), ('c', [3, 8])]
```



### 4.2.5 mapValue

* 针对kv元组型RDD
* 保持key不动，对每一个元组的value进行操作

```py
rdd1 = sc.parallelize([('a', 1), ('b', 2), ('c', 3), ('a', 2), ('b', 4), ('c', 8)], 3)
rdd2 = rdd1.mapValues(lambda x: x * 10)
print(rdd2.collect())

// 结果
[('a', 10), ('b', 20), ('c', 30), ('a', 20), ('b', 40), ('c', 80)]
```

### 4.2.6 groupBy

* 对kv元组型RDD使用
* 根据key或value进行分组
* 不改变value值
* 注意和groupByKey区别

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([('a', 1), ('a', 2), ('b', 2), ('b', 3), ('c', 3)], 3)
    # 按照key分组：lambda x:x[0]   按照value分组：lambda x:x[1]
    rdd2 = rdd1.groupBy(lambda x: x[0])
    print(rdd2.collect())
    
    
// 结果
[('b', <pyspark.resultiterable.ResultIterable object at 0x7f4e84853970>), 
 ('a', <pyspark.resultiterable.ResultIterable object at 0x7f4e84853b80>), 
 ('c', <pyspark.resultiterable.ResultIterable object at 0x7f4e84853c40>)]


// 此时返回value为可迭代对象，强制转换为list显示
print(rdd2.map(lambda x: (x[0], list(x[1]))).collect())
// 结果
[('b', [('b', 2), ('b', 3)]), ('a', [('a', 1), ('a', 2)]), ('c', [('c', 3)])]
```

### 4.2.7 filter

* 用来判断和过滤
* 执行filter()内的函数判断语句
* 返回TRUE或FALSE
* 保留TRUE生成新的RDD

```py
# 筛选数字
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)
	# 返回奇数
    rdd1 = sc.parallelize([1, 2, 3, 4, 5, 6], 3)
    rdd2 = rdd1.filter(lambda x: x % 2 == 1)
    print(rdd2.collect())
```

```py
# 筛选字典
from pyspark import SparkConf, SparkContext
import json

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    # 读取json文件为字符串
    file_rdd = sc.textFile("../data/input/order.text", 3)
    # 分割为单个json字符串型的字典
    json_rdd = file_rdd.flatMap(lambda line: line.split('|'))
    # 所有的json字符串转为字典
    dict_rdd = json_rdd.map(lambda i: json.loads(i))
    # 筛选字典
    select_rdd = dict_rdd.filter(lambda i: i["areaName"] == "北京")
    # 输出结果
    res_rdd = select_rdd.map(lambda i: i["areaName"]+'_'+i["category"])
    print(res_rdd.collect())
```

```py
# 过滤掉指定字符
set = ['a','b','c']
broadcast = sc.broadcast(set)

def filter_char(data):
    broadcast_char = broadcast.value
    if data in broadcast_char:
        return False
    else:
        return True

word_rdd = words_rdd.filter(filter_char)
```



### 4.2.8 distinct

* 去重
* 无参数

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([1, 1, 1, 2, 2, 3, 3, 4, 5, 6, 6, 7], 3)
    rdd2 = rdd1.distinct()
    print(rdd2.collect())

// 结果
[3, 6, 1, 4, 7, 2, 5]
```

### 4.2.9 union

* 合并任何格式的RDD
* 不去重

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([1, 1, 2, 3], 3)
    rdd2 = sc.parallelize([('a', 1), ('b', 2)], 3)
    rdd3 = sc.parallelize(['n', 'n', 'q'])
    rdd4 = rdd1.union(rdd2).union(rdd3)
    print(rdd4.collect())

```

### 4.2.10 join

* 用于kv元组型RDD
* 只按照key值关联两个RDD
* join内连接，leftOuterJoin左外连接

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([(1001, '张三'), (1002, '李四'), (1003, '王五')], 3)
    rdd2 = sc.parallelize([(1001, "销售"), (1004, "主席团")], 3)

    rdd3 = rdd1.leftOuterJoin(rdd2)
    print(rdd3.collect())

    rdd4 = rdd1.join(rdd2)
    print(rdd4.collect())
    
// 结果
[(1002, ('李四', None)), (1003, ('王五', None)), (1001, ('张三', '销售'))]
[(1001, ('张三', '销售'))]
```

### 4.2.11 interSection

* 求两个RDD的交集

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([(1001, '张三'), (1002, '李四'), (1003, '王五')], 3)
    rdd2 = sc.parallelize([(1001, "张三"), (1004, "主席团")], 3)

    rdd3 = rdd1.intersection(rdd2)
    print(rdd3.collect())

// 结果
[(1001, '张三')]
```

### 4.2.12 glom

* 查看分区状况

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([1, 2, 3, 4, 5, 6, 7, 8, 9], 3)
    # 查看嵌套
    rdd2 = rdd1.glom()
    print(rdd2.collect())

    # 解嵌套
    rdd3 = rdd2.flatMap(lambda x: x)
    print(rdd3.collect())

```

### 4.2.13 sortBy

* 用于kv型RDD
* 可按照key 或 value升序或者降序
* 三个参数

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([('a', 3), ('r', 7), ('d', 1), ('q', 2), ('c', 5)], 3)
    
	# 按照key：x[0]/value:x[1]排序， ascending升序， 为保证全局有序，numPartition设置为1
    rdd2 = rdd1.sortBy(lambda x: x[0], ascending=True, numPartitions=1)
    print(rdd2.collect())
    
    rdd3 = rdd1.sortBy(lambda x: x[1], ascending=True, numPartitions=1)
    print(rdd3.collect())

// 结果
[('a', 3), ('c', 5), ('d', 1), ('q', 2), ('r', 7)]
[('d', 1), ('q', 2), ('a', 3), ('c', 5), ('r', 7)]
```

### 4.2.14 sortByKey

* 用于kv型RDD
* 只能按照key排序
* 三个参数

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize([('a', 3), ('r', 7), ('d', 1), ('q', 2), ('c', 5), ('A', 4), ('E', 6)], 3)
    # 升序，分区，对key进行处理（不写默认不处理）
    rdd2 = rdd1.sortByKey(ascending=True, numPartitions=1)
    print(rdd2.collect())

// 结果
[('A', 4), ('E', 6), ('a', 3), ('c', 5), ('d', 1), ('q', 2), ('r', 7)]
    
    # 将key中大写字母强制转换为小写进行排序
	rdd2 = rdd1.sortByKey(ascending=True, numPartitions=1, keyfunc=lambda key: str(key).lower())
    print(rdd2.collect())

//结果
[('a', 3), ('A', 4), ('c', 5), ('d', 1), ('E', 6), ('q', 2), ('r', 7)]
```

## 4.3 Action算子

* 返回的不是RDD
* 显示不用collect收集

### 4.3.1 countByKey

* 用于kv元组rdd
* 统计 key 出现的次数

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    file_rdd = sc.textFile("../data/words.text", 3)
    kv_rdd = file_rdd.flatMap(lambda x: x.split(" ")).map(lambda x: (x, 1))

    res_rdd = kv_rdd.countByKey()
    print(res_rdd)
```

### 4.3.2 collect

### 4.3.3 reduce

* 聚合操作
* 传入函数，两个参数

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)

    rdd1 = sc.parallelize(range(1, 10))
    rdd2 = rdd1.reduce(lambda a, b: a + b)
    print(rdd2)
   
// 结果
45
```

### 4.3.4 flod

* 带有初始值聚合

```py
rdd1 = sc.paralelize([1,2,3])
# 10 + 1+2+3
rdd2 = rdd1.flod(10, lambda a, b : a+b)
```

### 4.3.5 first

* 取出第一个元素

```
sc.paralelize([1,2]).first()
[1]
```

### 4.3.6 take

* 取出前n个元素，list形式返回

```py
sc.paralelize([1,2,3]).take(2)
[1,2]
```

### 4.3.7 top

* 对RDD降序排列，去前n个，即最大的几个

```py
c.paralelize([1,2,3]).top(2)
[3,2]
```

### 4.3.8 takeOrdered

* 对RDD排序，取前几个,默认升序，即取最小的几个
* 参数1：要几个数据
* 参数2： 对数据进行操作，便于排序

```py
rdd.takeOrdered(3，lambda x : -x)
```

### 4.3.9 count

* 计算rdd内有多少个元素

```py
c.paralelize([1,2,3]).count
3
```

### 4.3.10 takeSample

+ 随机抽样显示

![image-20230224150037879](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224150037879.png)

```py
rdd = sc.paralelize([1,2.4,6,7])
print(rdd.takeSample(True,3))
```

### 4.3.11 foreach

* 对每一个数据进行操作，和map一样
* 没有返回值

```py
rdd2 = rdd.foreach(lambda x : print(x * 10))
print(rdd2)
```

### 4.3.12 saveAsTextFile

* 将RDD数据写入
* 本地、hdfs等

```
rdd = sc.paralelize([1,2,3])
rdd.saveAsTextFile("hdfs://node1:8020/xx/xx")
```

![image-20230224150826111](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224150826111.png)

 ### 4.3.13 注意

![image-20230224150935412](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224150935412.png)

  ## 4.4 分区操作算子











## 4.4 案例

### 4.4.1 wordsCount

```py
from pyspark import SparkConf, SparkContext

if __name__ == '__main__':
    conf = SparkConf().setAppName("test").setMaster("local[*]")
    sc = SparkContext(conf=conf)
    
    // 分行读取
    lines_rdd = sc.textFile("../data/words.text")
    // 以单词形式存储
    words_rdd = lines_rdd.flatMap(lambda x: x.split(" "))
    // 单词为key变为元组
    tuple_rdd = words_rdd.map(lambda x: (x, 1))
    // 分组聚合
    count_rdd = tuple_rdd.reduceByKey(lambda a, b: a + b)
    print(count_rdd.collect())

```

## 4.5 RDD的持久化

### 4.5.1 定义

* RDD的数据是过程数据，之间相互迭代，当生成新的RDD后，旧RDD就会被从内存中清理。
* 想要使用旧RDD就需要重新执行

![image-20230224131150367](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224131150367.png)



### 4.5.2 RDD缓存

* Spark提供缓存API，将指定RDD数据集保留

```py
# 导入包（点击persist源码复制）
from pyspark.storagelevel import StorageLevel
```

![image-20230224132102815](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224132102815.png)

![image-20230224133233934](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224133233934.png)

* 分散存储

### 4.5.3 RDD CheckPoint

![image-20230224133501857](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224133501857.png)

* 可以集中保存在HDFS中

![image-20230224133339188](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224133339188.png)

![image-20230224133400830](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224133400830.png)

### 4.5.4 对比

![image-20230224133641389](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230224133641389.png)

## 4.6 jieba库

```py
import jieba

content = "我是研究生我要读清华大学博士。"

print(list(jieba.cut(content))) # 精简模式，返回一个列表类型的结果
print(" ".join(jieba.lcut(content, True)))  # 全模式，使用 'cut_all=True' 指定
print(jieba.lcut_for_search(content)) # 搜索引擎模式

['我', '是', '研究生', '我要', '读', '清华大学', '博士', '。']
我 是 研究 研究生 我 要 读 清华 清华大学 华大 大学 学博 博士 。
['我', '是', '研究', '研究生', '我要', '读', '清华', '华大', '大学', '清华大学', '博士', '。']
```

# 五 共享变量

## 5.1 广播变量

### 5.1.1 场景

* 场景：本地单机list与分布式集群rdd交互

![image-20230225185804666](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225185804666.png)



![image-20230225185818462](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225185818462.png)



### 5.1.2 使用方式

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225185905067.png" alt="image-20230225185905067" style="zoom:50%;" />

![image-20230225185943715](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225185943715.png)

## 5.2 累加器

### 5.2.1 使用



![image-20230225192300816](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225192300816.png)

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225192350892.png" alt="image-20230225192350892" style="zoom:50%;" />

![image-20230225192425700](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225192425700.png)

### 5.2.3 注意事项

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225192553004.png" alt="image-20230225192553004" style="zoom:67%;" />



* 解决办法，保存rdd2值

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230225192636044.png" alt="image-20230225192636044"  />



## 5.3 案例

```
//txt
   hadoop spark # hadoop spark spark
mapreduce ! spark spark hive !
hive spark hadoop mapreduce spark %
   spark hive sql sql spark hive , hive spark !
!  hdfs hdfs mapreduce mapreduce spark hive

  #
```

```py
from pyspark import SparkConf, SparkContext
import re

if __name__ == '__main__':
    conf = SparkConf().setMaster("local[*]").setAppName("test")
    sc = SparkContext(conf=conf)

    charSet = [",", ".", "!", "#", "$", "%"]
    broadcast = sc.broadcast(charSet)

    count = sc.accumulator(0)

    content_rdd = sc.textFile("../data/input/accumulator_broadcast_data.txt")
    # 过滤空行
    no_strip_line_rdd = content_rdd.filter(lambda line: line.strip())

    # 过滤非空行前后的空格
    lines_rdd = no_strip_line_rdd.map(lambda line: line.strip())

    # 按照空格切割单词和字符,有单个或多个空格，使用正则字符串处理
    words_rdd = lines_rdd.flatMap(lambda x: re.split("\s+", x))
    print(words_rdd.collect())

    # 过滤掉字符并统计个数
    def filter_char(data):
        global count
        broadcast_char = broadcast.value
        if data in broadcast_char:
            count += 1
            return False
        else:
            return True

    word_rdd = words_rdd.filter(filter_char)

    # 转为元组，聚合统计排序
    result = word_rdd.map(lambda x: (x, 1)).\
        reduceByKey(lambda a, b: a + b).\
        collect()

    print(f"非法字符有{count}个")
    print("各个单词统计结果如下", result)
```

# 六 Spark内核调度

# 七 SparkSQL

## 7.1 DataFrame结构

![image-20230228203104547](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230228203104547.png)

![image-20230228201542619](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230228201542619.png)

## 7.2 DataFrame创建

### 7.2.1 基于RDD

* 待转化的RDD必须为嵌套形式（数组/kv元组类型），并且由字符串转化为对应的数值类型

* builder()配置如下

  <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230302113407900.png" alt="image-20230302113407900" style="zoom:80%;" />

```py
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StringType, IntegerType

if __name__ == '__main__':
    # 建立sparkSession对象
    spark = SparkSession.builder.appName("test").master("local[*]").getOrCreate()
    # 调用sparkContext对象
    sc = spark.sparkContext

    # 创建rdd对象，并将其分割，定义其数据类型
    # [('Michael', 29), ('Andy', 30), ('Justin', 19)]
    rdd = sc.textFile("../data/input/sql/people.txt").map(lambda x: x.split(",")).map(lambda x: [x[0], int(x[1])])
    # print(rdd.collect())

    # 法1：通过rdd转换创建dataFrame
    # 1.1 可以直接传入name构建
    df = spark.createDataFrame(rdd, schema=["name", "age"])
    # 1.2 构建完整的schema
    schema = StructType().add("name", StringType(), True).add("age", IntegerType(), True)
    df = spark.createDataFrame(rdd, schema=schema)
推荐 # 1.3 toDF构建 ----传入列表
    df = rdd.toDF(["name", "age"])
    # 1.4 完整传参
    schema = StructType().add("name", StringType(), True).add("age", IntegerType(), True)
    df = rdd.toDF(schema)

    # 显示表结构
    df.printSchema()
    # 显示df表，参数1： 显示n个数据，默认显示20个  参数2： 是否截断，即默认显示20个，之后的省略，默认截断
    df.show(20, False)
```

### 7.2.2 基于pandas

```py
from pyspark.sql import SparkSession
import pandas as pd

if __name__ == '__main__':
    # 建立sparkSession对象
    spark = SparkSession.builder.appName("test").master("local[*]").getOrCreate()
    
    # 2.基于pandas构建dataframe
    pdf = pd.DataFrame({
        "name": ["张三", "李四"],
        "age": [11, 21]
    })
    df = spark.createDataFrame(pdf)
    # 显示表结构
    df.printSchema()
    # 显示df表，参数1： 显示n个数据，默认显示20个  参数2： 是否截断，即默认显示20个，之后的省略，默认截断
    df.show(20, False)
```

### 7.3 创建总结

```py
# rdd(嵌套类)转化---必须传入list，只有段名
rdd.toDF(["name","age"])

# 读取文件转化---chema中传入str，包含段名+类型
df.read.format("csv").schema("段1 INT， 段2 STRING， 段3 INT").load("../../..")
```



## 7.3 读取文件

![image-20230228205710051](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230228205710051.png)

### 7.3.0 schema的书写格式

```py
# 1、完整格式
schema = StructType().add("列名表头",IntergerType(),True)
df = read().format().schema(schema = shcema).load()

# 2、简写----一个字符串内，多个字段名逗号隔开
df = read().format().schema("name STRING, age INT").load()
```



### 7.3.1 读取text文件

* 按行读取
* 只有一列数据，默认表头为value

```py
schema = StructType().add("data", StringType(), True)
    df = spark.read.format("text").\
        schema(schema=schema).\
        load("../data/input/sql/people.txt")
```

### 7.3.2 读取csv文件

* 用逗号隔开的文件
* .txt  .csv

```py
df = spark.read.format("csv"). \
        option("sep", "\t"). \         # 设置分隔符
        option("header", False). \     # 文件中是否是表头
        option("encoding", "utf-8"). \ # 编码
        schema(schema=schema).load("../data/input/sql/u.data")
```

### 7.3.3 读取json文件

* josn文件格式（字典）`{"name":"Michael"}`
* 自带schema信息，所以导入时不用定义schema

```py
df = spark.read.format("json").load("../data/input/sql/people.json")
```

### 7.3.4读取parquet文件

* parquet文件，自带schema信息

```py
df = spark.read.format("parquet").load("../data/input/sql/people.parquet")
```



## 7.3 风格

### 7.3.1 SQL风格

```py
# 先将datafram处理成可视化表
df.createOrReplaceTempView("data")
df.createTempView("data")  # 临时表，只能在本session下使用
df.createGlobalTempView("data") # 全局表，可以在全部session使用
```

```py
spark.sql("SELECT * FROM data").show()
```

### 7.3.2 DSL风格

## 7.4 DSL语法

* 拉取数据

  用基本SQL从数据源中拉出数据，计算交给Spark做

* 创建datafrom
* 计算
* 存储数据

### 7.4.1 拉取数据

```java

import org.apache.spark.sql.SparkSession

object HiveWrite {
  /**
   * 创建方式1
   */
  def demo01(spark: SparkSession): Unit = {
    // 先创建换一个数据库
    spark.sql("show databases").show
    spark.sql("create database ceshi").show // 如果数据库存在会抛异常
    spark.sql("use ceshi").show
    spark.sql("create table user1(id int, name string)").show //如果表存在会抛异常
    spark.sql("insert into user1 values(10, 'lisi')").show
  }

  /**
   * 创建方式2
   *
   * @param spark
   */
  def demo02(spark: SparkSession): Unit = {
    spark.sql("show databases").show
    spark.sql("create database ceshi2").show // 如果数据库存在会抛异常
    //读取json里面的数据,转成DataFrame
    val df = spark.read.json("E:\\ZJJ_SparkSQL\\demo01\\src\\main\\resources\\users.json")
    spark.sql("use ceshi2")
    //    // 直接把数据写入到hive中. 表可以存着也可以不存在
    df.write.mode("append").saveAsTable("user2")
    df.write.insertInto("user2") // 基本等于 mode("append").saveAsTable("user2")
  }

  def main(args: Array[String]): Unit = {
    //  设置登录人,不然会出现没有权限问题
    System.setProperty("HADOOP_USER_NAME", "root")
    val spark: SparkSession = SparkSession
      .builder()
      .master("local[*]")
      .appName("HiveWrite")
      .enableHiveSupport()
      // 指定Hive的数据仓库的位置,这个路径是hdfs上面的路径
      .config("spark.sql.warehouse.dir", "hdfs://zjj101:9000/user/hive/warehouse")
      .getOrCreate()
    //    demo01(spark) // 创建数据库和表插入数据方式1
    demo02(spark) // 创建数据库和表插入数据方式1


    spark.close()


  }
}

```



###7.4.2 数据探索

```py
1、展示
# 可以指定打印的信息，默认20
df.show(5)

2、统计行数
df.count()

3、查看表头信息
df.shcema()

4、查看字段数据
df.columns

5、查看字段类型
df.dtypes
```

### 7.4.3 基本数据处理

```py
1、 查询
df.select("age") # 列名
df.select(df.age) # 列值
# 查询多个字段，用列表输入，或者用索引输入
df.select(['age','name'])  #.show看结果
df.select(df["age"], df["name"])
# 全部显示
df.show()
# 可以混搭
df.select([df["id"], "name"]).show()

# 注意：
不写select，则返回语句中 出现或新增 的所有列，写select，则仅返回select中要求的列

3、筛选
df.filter(df.name == "Alice")
df.where("name == 'Alice'")
df.where(df["name"] == "Alice") # 推荐
# 2、多组条件 在一个引号里面
df.where('name == "Alice" and gender == "男"')
# 3、比较值为常量
df.where("age" > 3)
# 4、比较值为嵌套查询语句，需要将其从df转为 值
df.where(df["rank"] > df.select(F.avg("rank").alias("avg_rank")).first()["avg_rank"])

4、分组计算+聚合函数+重命名函数
df.groupby("gender").sum("age")
# select gender, sun(age) from df groupby gender
# 多个分组，用列表输入
df.groupby(["gender","class"]).avg("age")
# select gender, class, sun(age) from df groupby gender


5、过滤-分组聚合
# spark不提供having，仍用where
# 注意where内的是字段名，不含引号
df.groupby(["gender","class"]).avg("age").where('avg(age)>20')


6、 排序
df.orderby("age", asscending=False)
# select * from df orderby age desc
# 多字段排序，只能按照一个规则排序
df.orderby(["age","id"], asscending=False)
# select * from df orderby age, id desc

7、 limit
# 只能返回n条数据，不能分页
df.limit(5)

8、去重
df.distinct()

9、执行顺序
df.where("age" == 10).
groupby(["class","gender"]).avg("age").
where("avg(age)>30").
orderby("avg(age)").
limit(5)
```

### 7.4.4 高级数据处理

```py
1、join
# 表名不用引用号
# 默认inner连接
df1.join(df2,'id')
df1.join(df2,'id','right')
df1.join(df2,'id','left')


2、缓存到磁盘
df.persist()

3、checkpoint到hdfs
# 设置存储路径
sc.setCheckpointDir("hdfs://node1:8020/xxxx")
df.checkpoint()

4、 列操作withcolumn（增、删、改）
# 获取列
id_column = df.id
id_column = df["id"]
df.select(id_column)
# 4.1 更改列的数据类型
df2 = df.withColumn("id", df.id.cast("String"))
df2.printSchema()
# 4.2 转换/更改现有列的值（对该列进行操作）
df4 = df.withColumn("id", df.id + 1)
# 4.3 从现有列派生新列
df4 = df.withColumn("newcolumn", df.id + 1)
# 4.4 覆盖写/创建新列
df5 = df.withColumn("name", F.lit("USA"))
# 4.5 重命名列名
df6 = df.withColumnRenamed("name", "newname")

5、获取到df中某一个单元格的值
# 先limit取出整行，用first()[" 所需要的单元格的列名"] 取出具体的值


5、连接两张表到一张表，同字段
df.unionAll(df2)

6、删除重复记录
df.drop_duplicates()

7、删除列
df.drop("id")

8、删除存在缺失值的记录
# 传入一个list，删除指定字缺失的记录
df.dropna(subset=['age','name'])

9、填补缺失值
# 传入一个dict，对指定字段进行填充
df.fillna({'age':10, 'name':'abc'})

```



### 7.4.5 内置函数（funtions）

* 聚合函数（有分组必有聚合，有聚合未必分组）
  * 1、直接使用聚合函数,只能聚合一次

```py
df.groupby("gendder").max("age")
# 新增列名为count，用withcolumrename()改名
```

​          2、 使用内置聚合函数 + agg配合使用，可以聚合多次

```py
df.groupby("gendder").agg(F.max("age"))
df.groupby("gendder").agg(F.max("age"), F.avg("age"))
# 新增count(id) ，用F.alias()改名
df.select(F.count(df.age))
```

* 对计算后的字段起别名

```
# 注意，必须要跟在内置函数后，即F.xxx.alias()
df.groupby("gender").agg(F.round(F.avg("age"),2).alias("平均值"))
```

* 覆盖写lit

```py
df.withcolumn("niew", F.lit(1))
```

* explode + split 

```py
# 按空格切分单词
df1 = df.withColumn("value", F.explode(F.split(df.value, " ")))
```

* 字符串，用来操作每个列的关系

```py
# 1、拼接
df.select(F.concat('name','gender'))

# 2、拼接符拼接 （可用于将所有的列拼接为一列）
df.select(F.concat_ws(',','name','gender'))

# 3、按字符长度截取
df.select(F.substring("name",1,4)) # 从第一位开始截取4位

# 4、按字符切割
df.select(F.split("name",'-'))

# 5、切割后为列表，可按下标取值
df.select(F.split("name",'-')[0])
```

* 时间

```py
# 1、获取当前日期
df.select(F.current_data())
# 2、获取当前时间
df.select(F.current_timestamp())
# 3、获取当前unix信息
df.select(F.unix_timestamp())
# 4、将unix时间转为标准时间
df.select(F.from_unixtime("unix",format="yyyy-MM-dd HH:mm:ss"))
# 5、时间加减
df.select(F.data_add("time",1))
```

* 保留小数

```py
df.groupby("gender").agg(F.round(F.avg("age")),2)
```

### 7.4.6 数据清洗

```py
# 全局去重
df.dropDuplicates()
# 局部去重
df.dropDuplicates(["age","name"])
```

```py
# 全局缺,只要有null值，删除整行
df.dropna()
# thresh=3 : 最少满足三个有效列，不满足则删除
df.dropna(thresh=3)
# 指定列有缺失值，就删除
df.dropna(subset=["age","name"])
```

```py
# 全局缺失值填充
df.fillna("loss")
# 指定列填充
df.fillna("loss",subset=["name"])
# 多个列填充，用字典定义
df.fillna({"name":"未知", "age":1, "job":"worker"})
```

### 7.4.5 数据写出

![image-20230309094258322](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230309094258322.png)

* 存为text

  * 按照一行 写出，写出前要将所有列存为一行,然后输出

  ```py
  df.select(F.concat_ws("----------", "user_id", "movie_id", "rank", "time")). \
  write. \
  mode("overwrite"). \
  format("text"). \
  save("../data/output/text")
  ```

* 存为csv

  ```py
  df.write.mode("overwrite"). \
  format("csv"). \
  option("sep", ",").\
  option("header",True).\
  save("../data/output/csv")
  ```

* 存为json

  ```py
  df.write.mode("overwrite").\
  format("json").\
  save("../data/output/json")
  ```

* 存为parquet

  ```py
  df.write.mode("overwrite").\
  format("parquet").\
  save("../data/output/parquet")
  ```

* 注意，存储完之后，要将pycharm相关文件与服务器内容同步

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230309100336410.png" alt="image-20230309100336410" style="zoom: 80%;" />

* jdbc连接数据库

```py
df.write.mode("overwrite"). \
        format("jdbc"). \
        option("url", "jdbc:mysql://127.0.0.1:3306/bigdata"). \
        option("dbtable", "movie_data"). \
        option("user", "root"). \
        option("password", "123456"). \
        save()
```



### 7.4.6 查询案例

```py
# 查询大于平均分的电影的数量  //select返回一个平均值为df对象，用first取出，再用索引取出值
print("查询大于平均分的电影的数量:", df.where(df["rank"]>df.select(F.avg("rank")).first()["avg(rank)"]).count())

# 查询高分电影中打分最多的用户
user_id = df.where(df["rank"] > 3). \
        groupBy("user_id"). \
        count(). \
        withColumnRenamed("count", "cnt"). \
        orderBy("cnt", ascending=False). \
        limit(1). \
        first()["user_id"]
# 查询某人的电影评分值
df.where(df["user_id"] == user_id).select(F.round(F.avg("rank"))).show()
# 查询被评分超过100次的电影的平均分，返回排名TOP10
    df.groupBy("movie_id").\
        agg(F.count("user_id").alias("cnt"),
            F.round(F.avg("rank"), 2).alias("avg_rank")).\
        where("cnt > 100").\
        orderBy("avg_rank", ascending=False).\
        limit(10).select("avg_rank").show()
```





















# 9 端口集合

网页

1.hdfs：node1:9870  网页查询

2.yarn node1:8088  

3.spark  node1:8080

4.spark 子进程：node1：4040

5.hive  node1:1000

访问端口

hdfs://node1:8020/xxx

jdbc:mysql://node1:3306/xxx



