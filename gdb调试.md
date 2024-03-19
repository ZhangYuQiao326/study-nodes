![image-20240319115452359](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240319115452359.png)

## 开启

```shell
// -g 生成调试文件
g++ text.cpp -o test -g
// 开始调试
gdb test
// 结束调试
quit   (q)
```



## 查看源码

```shell
// list + 行号范围
l 1,10

// list + 函数名
l func

// 查看所有源码,回车继续加载
l 1,
```



## 断点

* break打断点

```shell
// 1. 单个文件 break 行号
b 3

// 2. 多个文件 break 文件:行号
b test.c:6

```

* info 查看所有断点breakpoint

```cpp
info b
```

* 删除指定断点delete  断点num

```shell
d 1
```



## 执行

```shell
1、next命令（可简写为n）用于在程序断住后，继续执行下一条语句。
2、step命令（可简写为s），它可以单步跟踪到函数内部。
3、continue命令（可简写为c）或者fg，它会继续执行程序，直到再次遇到断点处。

4 finsh 跳出函数内部，继续外部执行
```

