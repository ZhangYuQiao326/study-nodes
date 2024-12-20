开发利器：Zeppelin NoteBook

Flink-loacl部署方法：https://dblab.xmu.edu.cn/blog/4340/

kafka-loacl部署：https://blog.51cto.com/u_16099280/10603933

# 1 环境部署

## 1.1 安装java11

```shell
sudo apt update
sudo apt install openjdk-11-jdk

# 环境变量
sudo vim ~/.bashrc
# 添加
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

# 更新
source ~/.bashrc

# 验证
java -version

openjdk version "11.0.25" 2024-10-15
OpenJDK Runtime Environment (build 11.0.25+9-post-Ubuntu-1ubuntu120.04)
OpenJDK 64-Bit Server VM (build 11.0.25+9-post-Ubuntu-1ubuntu120.04, mixed mode, sharing)

```

## 1.2 Flink-loacl

1 网盘下载flink-1.16.2-bin-scala_2.12.tgz安装包， /me/计算机/安装包

2 上传服务器- autodl-4090

3 解压缩 `sudo tar -zxvf flink-1.16.2-bin-scala_2.12.tgz -C /usr/local`

4 重命名+权限

```shell
cd /usr/local
sudo mv ./ flink-1.16.2 ./flink
sudo chown -R root:root ./flink
```

5 环境变量

```shell
vim ~/.bashrc

export FLINK_HOME=/usr/local/flink
export PATH=$FLINK_HOME/bin:$PATH

source ~/.bashrc
```

6 启动flink服务

```shell
cd /usr/local/flink
./bin/start-cluster.sh

# 关闭
./bin/stop-cluster.sh
```

7 jps验证启动

```shell
$ jps
8660 TaskManagerRunner
9333 Jps
8383 StandaloneSessionClusterEntrypoint
```

8 测试

```shell
cd /usr/local/flink/bin
./flink run /usr/local/flink/examples/batch/WordCount.jar
```

```
Starting execution of program
Executing WordCount example with default input data set.
Use --input to specify file input.
Printing result to stdout. Use --output to specify output path.
(a,5)
(action,1)
(after,1)
(against,1)
(all,2)
```

9 pyflink安装

```shell
# 要求3.8以上
python --version
# 安装
python -m pip install apache-flink==1.16.2

# 验证
pip show apache-flink

```

```
Name: apache-flink
Version: 1.16.2
Summary: Apache Flink Python API
Home-page: https://flink.apache.org
Author: Apache Software Foundation
Author-email: dev@flink.apache.org
License: https://www.apache.org/licenses/LICENSE-2.0
Location: /root/miniconda3/lib/python3.8/site-packages
Requires: numpy, apache-beam, pemja, cloudpickle, python-dateutil, pandas, requests, pytz, fastavro, httplib2, py4j, protobuf, pyarrow, apache-flink-libraries, avro-python3
Required-by: 
```

```python
# 也可以用代码验证
from pyflink.table import EnvironmentSettings, TableEnvironment

# 创建环境设置，启用批处理模式
env_settings = EnvironmentSettings.new_instance().in_batch_mode().build()

# 创建 TableEnvironment 实例
t_env = TableEnvironment.create(env_settings)

# 打印版本信息，验证 PyFlink 是否正常工作
print(f"PyFlink version: {t_env.get_config().get_configuration().get_string('pipeline.jars', 'No Jars')}")

# 结束
t_env.execute_sql("SELECT 'Hello, PyFlink!'")

```

```
执行后显示：
PyFlink version: No Jars
```

## 1.3 Kafka-local

1 安装包下载：https://archive.apache.org/dist/kafka/2.5.0/kafka_2.12-2.5.0.tgz，或者网盘：me/计算机/安装包

2 上传服务器：/root/auto-tmp/mypacket/Kafka, 解压

```cpp
tar -zxvf kafka 2.12-2.5.0.tgz
```

3 (无需修改，需要了解)kafka需要安装zookeeper使用，但kafka集成zookeeper,在单机搭建时可直接使用。使用需配置kafka_2.12-2.5.0/conig 下的"zookeeper.properties”

```shell
vim kafka 2.12-2.5.0/config/zookeeper.properties
```

配置"zookeeper.properies"。修改dataDir和clientPor，前者是快照存放地址(自己随意配置)，后者是客户端连接zookeeper服务的端默认端口2181 最好默认不修改

![image-20241213115044728](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20241213115044728.png)



4 配置kafka 2.12-2.5.0/config下的“server.properties”，接地址(端口和clientPort保持修改log.dirs和zookeeper.connect。

前者是日志存放文件夹，后者是zookeeper连接地址

```shell
vim kafka 2.12-2.5.0/config/server.properties
```

* 修改日志地址

```
log.dir = /root/auto-tmp/mypacket/Kafka/log
```

* zookeeper连接地址无需修改

```
zookeeper.connect=localhost:2181
```

5 开启指令

* zookeeper指令

```
前台执行
./bin/zookeeper-server-start.sh ./config/zookeeper.properties
./bin/zookeeper-server-stop.sh ./config/zookeeper.properties

后台执行
./bin/zookeeper-server-start.sh -daemon ./config/zookeeper.properties
```

* kafka开始

```
前台执行
.bin/kafka-server-start.sh ./config/server.properties
.bin/kafka-server-stop.sh ./config/server.properties  （优先使用）

后台执行
bin/kafka-server-start.sh -daemon ./config/server.properties
```

```
关闭
kill -9 <PID>
```



6 验证

```shell
[root@192 config]# jps83479 JpS
3662 QuorumPeerMain
4014 Kafka
```

# 2 相关指令

## 2.1 Kafka

1. 创建kafka主题

```shell
# 远程操作的话：localhost替换为服务器ip
# 创建test主题
./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

```

2. 显示所有kafka主题

```shell
./bin/kafka-topics.sh --list --zookeeper localhost:2181
--test
```

3. 查看某主题的详细信息

```shell
./bin/kafka-topics.sh  --zookeeper localhost:2181 --describe ? --topic test
Topic: test     PartitionCount: 1       ReplicationFactor: 1    Configs: 
Topic: test     Partition: 0    Leader: 0       Replicas: 0     Isr: 0

```

4 创建生产者，自定义端口9092

```shell
./bin/kafka-console-producer.sh  --broker-list localhost:9092 --topic test

```

5 创建消费者， 定义端口9092，进行通讯

```shell
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning

```

--from-beginning: 从头消费

![image-20241213120859214](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20241213120859214.png)



