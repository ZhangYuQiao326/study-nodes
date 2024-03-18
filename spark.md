idea 环境搭建

1 下载好 java scala spark hadoop

https://dr34m.gitee.io/2022/01/newpost-31/

2 idea中下载scala插件

3 文件-项目结构中

模块-加入Scala-jdk

4 项目中的给具体的模块-加入scala框架 - 才能新建scala类

5 target目录中-pom.xml文件中，加入scala spark依赖，注意刷新maven文件，当不变红说明导入成功

```xml
<dependencies>
 <dependency>
 <groupId>org.apache.spark</groupId>
 <artifactId>spark-core_2.12</artifactId>
 <version>3.0.0</version>
 </dependency>
</dependencies>
```

