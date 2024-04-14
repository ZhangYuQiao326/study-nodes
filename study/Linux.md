##  1 开机/关机/重启

```
halt
shutdown -h now
shutdown -h 1

reboot
shutdown -r now

sync
```

## 2 用户
### 1 创建/登录/注销（xshell）

```
useradd tom
passwd tom   xxx

#切换用户
su - root
123456

# 退出用户
logout  ctrl+d

# 删除用户
userdel tom   #仅删除用户
userdel -r tom # 删除用户和home目录
```

### 2 查看信息

```
who am i # 查看用户名
pwd #查看当前路径
id 用户名 # 查看用户信息
```

### 3 用户组

```
# 创建组
groupadd 组名

# 增加用户到组
useradd -g 组名 用户名

# 用户切换组
usermod -g 新组名 用户名
```

```
/etc/passwd  #用户配置文件
/etc/shadow  #口令配置文件
/etc/group   #组配置文件
```

## 2 实用指令

### 1 系统级别

```
# 切换系统级别（常用3[multi-user.target] 5[graphical.target]）
init 3/init 5

# 更换默认系统级别
systemctl set-default xxxxx(级别名)
```

### 2 找回root密码

### 3 帮助指令

```
man 指令
#隐藏文件以 . 开头
```

###  4 目录指令

```
# 查看当前路径
pwd
```

```
# 显示当前所有目录
ls 
la #显示隐藏
ll # 列表方式显示
```

```
# cd指令
cd ~ #切换到家目录[root用户家目录为/root][zhang用户家目录为/home/zhang]
cd .. #返回上一级[根目录和家目录并不一样]
cd zhang  #相对路径直接进入下一级，不用 cd /zhang
```

注意：当要多层次进入目录时： 

从==根目录==出发，cd /home/zhang

从 /home 出发 ，cd zhang

---------------

```
# 创建目录
mkdir xxx
mkdir -p xxx/xxx

# 删除空目录
rmdir xxx
#删除文件或者目录
rm xxx
rm -r xxx  删除目录及其子文件
rm -f xxx  强制删除
# 删除非空目录
rm -rf xxx/xxx
```

```
# 创建空文件
touch xxx
```

---------------

```
# 复制单个文件
cp 文件名 到目录名

# 复制目录（内含多个文件）
cp -r /home/dddd /opt

# 强制覆盖
/cp -r /home/dddd /opt
```

-------------

```
# 重命名/移动文件（剪切）or目录
mv xxx  xxx
```

### 5 查看文件指令
```
# 查看文件内容，修改文件内容需要vim
cat指令
cat -n 显示行号
cat /etc/profile

more指令(有交互命令，方便查看文件)
more /etc/profile

# cat 可以和 管道命令 more 一起运用
cat -n /etc/profile | more
```

![image-20221104111940846](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20221104111940846.png)

---------------
```
less程序（查看大型文件 指令更加强大）
less /home/xx
```

![image-20221104113309626](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20221104113309626.png)

----

```
echo指令
1 可以输出环境变量
echo $PATH 
2 打印(写)功能
echo "hello"
```

---

```
# 查看文件前n行内容（默认10行）
head /opt/xxx
head -n 5 /opt/xxx
```
```
# tail命令
# 查看文件后n行（默认后10行）
tail /opt/xxx
tail -n 5 /opt/xxx
tail -f (实时追踪某文档的更新)

```

### 6  > 命令和 >> 命令

```
1) > 命令为覆盖写， >>命令为追加写
2）ll /xxx/xxx > /xx/xx/文件            将列表写入新文件
3）cat /xx/xx >> /xx/xx/文件            将文件1内容追写到文件2
4）echo "新内容" >> /xx/xx/文件          将新内容追写到文件
5）cal >> /xx/xx/文件                   将日历信息追写到文件

```

### 7 创建快捷方式（软连接）

```
ln -s [原文件目录/名] [软连接目录/名字]
# 例如 给/home下添加/root的快捷方式
ln -s /root /home/myroot

# 删除快捷方式（软连接）
rm /home/myroot
```
###8 时间类型指令

```
date #显示当前时间
date "+%Y-%m-%d %H:%M:%S" #显示年月日时分秒
```

```
# 设置系统时间
date -s "2000-3-26 3:30:12"
```

```
# 日历
cal  #默认显示一月
cal 2022
```

### 9 搜索查找类指令

```
# 3种查找方式
find / -name [指定路径文件名]
find / -user [用户名]
find / -size [文件大小]   +n 大于 -n 小于
```

```
# 定位locate 指令
# 由于基于数据库查询，必须每次查询前先执行
updatedb
locate hello.txt
```

### 10 grep命令和管道符

```
grep [查找字] [路径文件] # 过滤查找
选项：grep -n 显示匹配行及行号
     grep -i 查找时候忽略字母大小写
x | y 管道符：将x的内容交给y进行二次处理

# 例：再hello。txt中，查找“yes”所在行，并且显示行号
法1 ： grep -n "yes" /home/hello.txt
法2 :  cat /home/hello.txt | grep -n "yes"

```

### 11 压缩/解压指令

```
# 压缩单个文件，xxx.gz
gzip /home/hello.txt
# 解压单个文件
gunzip /home/hello.txt.gz
```

```
# 压缩文件夹  xxx.zip
zip -r [xxx.zip] [压缩的文件路径]
::: -r 递归压缩文件夹 压缩重命名
# 解压文件夹
unzip xxx.zip
unzip -d [解压后存放路径][压缩包路径.zip]
```

```
# 同时打包多个目录 xxx.tar.gz
tar -zcvf [重命名.tar.gz][打包目录1][打包目录2]

# 解压到当前路径
tar -zxvf [xxx.tar.gz]

# 解压到指定路径
tar -zxvf [xxx.tar.gz] -C [指定路径]
```

### 网络

```shell
// 显示网络tcp连接，显示服务器版本等待客户端连接的应用
sudo netstat -tanp
```



## 3 用户权限

### 1 组管理

```
所有者 所在组 其他组
创建用户，默认属于自己组
用户创建文件，默认为文件的所有者，用户组为所在组
```

```
# 创建组
groupadd 组名

# 创建用户属于组
useradd -g 组名 用户名

# 修改用户所在组
usermod -g 新组名 用户名
```

```
# 查看文件/目录所在组
ls -ahl

# 修改文件所有者
chown [新拥有者用户名] [文件路径名]

# 修改文件所在组
chgrp [新组名] [文件名]
```

### 2 权限

`chmod`（change mode）命令在Unix和类Unix系统中用于改变文件或目录的权限。通过`chmod`，用户可以控制文件或目录的访问权限。这些权限分为三组：文件所有者（u），同组用户（g），其他用户（o）。权限类型包括读（r）、写（w）、执行（x）。`chmod`可以使用符号表示法或数字八进制表示法来设置权限。

### 符号表示法
- `+` 增加一个权限。
- `-` 删除一个权限。
- `=` 设置确切的权限，覆盖之前的设置。

权限指定给谁：
- `u` 文件的所有者。
- `g` 文件的所属群组。
- `o` 其他人。
- `a` 所有人（默认）。

权限类型：
- `r` 读权限。
- `w` 写权限。
- `x` 执行权限。

#### 示例
- **给所有者增加执行权限**：
  ```bash
  chmod u+x filename
  ```
- **给所有者和群组增加读和写权限**：
  ```bash
  chmod ug+rw filename
  ```
- **移除其他人的所有权限**：
  ```bash
  chmod o-rwx filename
  ```
- **给所有用户设置读和执行权限**：
  ```bash
  chmod a+rx filename
  ```
- **设置文件为所有者可读写执行，组和其他人只读**：
  ```bash
  chmod u=rwx,go=r filename
  ```

### 数字表示法（八进制）
每个权限类型分配一个数值：读（4）、写（2）、执行（1）。将这些值相加来获得特定组的权限数值。这三个数字分别代表所有者、组、其他人的权限。

#### 示例
- **设置所有者可读写执行（7），组和其他人可读执行（5）**：
  ```bash
  chmod 755 filename
  ```
- **设置所有者可读写（6），组和其他人无权限（0）**：
  ```bash
  chmod 600 filename
  ```
- **所有用户可读写执行（7）**：
  ```bash
  chmod 777 filename
  ```
- **所有者可读写（6），组可读（4），其他人无权限（0）**：
  ```bash
  chmod 640 filename
  ```

### 递归修改权限
- `-R` 或 `--recursive` 选项递归地改变指定目录及其子目录中所有文件的权限。

#### 示例
- **递归地给所有者增加执行权限**：
  ```bash
  chmod -R u+x directory_name
  ```
- **递归设置目录及其子目录所有文件为所有人可读**：
  ```bash
  chmod -R a+r directory_name
  ```

使用`chmod`时，选择符号表示法还是数字表示法取决于具体任务。符号表示法在单次修改少数权限时更为直观，而数字表示法在需要设置精确权限模式时更为高效。

```
-rwxrw-r--  root  root  1213  Feb  2 09:39 abc
```

![image-20221109165601036](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20221109165601036.png)



![image-20221109165644820](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20221109165644820.png)



![image-20221109165656471](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20221109165656471.png)

# 4 vmWare安装ubuntu22

iso镜像文件路径："D:\VM\Ubuntu22\ubuntu-22.04.2-desktop-amd64.iso"

vm安装路径：“D:\VM\Ubuntu22”

配置硬件

配置软件

* 配置c++

```
sudo apt update
// 网络工具
apt install net-tools

// 编译器
sudo apt install cmake
sudo apt install gcc
sudo apt install g++
sudo apt install clang
sudo apt install cmake clang-12 gdb

sudo apt install vim
```

* 配置git

  ```
  sudo apt install git
  git config --global user.name "ZhangYuQiao326"
  git config --global user.email "1347649631@qq.com"
  
  // 生成公私密钥（windows和linux下命令一致）
  ssh-keygen -C "1347649631@qq.com" -t rsa
  确认保存路径(/root/.ssh/id_rsa)(/c/Users/zhang/.ssh/id_rsa)
  输入密码 123456
  确认密码 123456
  公钥位置：cat /root/.ssh/id_rsa.pub
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDSCb7oIEU6aheEz3z4iFjYQrfGLgMzx2jRliwqVUGOg+GDhbMjLLzfFdp8N7eJ3MYlS2byZaZE3qeYOdqROq3HZxV/eQBUiIBT/uk9vEa3P2YlaSWmSLb+T0ZO1UGfoH2+TG+zXlf92mc8o9N8oKTqysnFBeQaDtMSgw81N6AfoatMVqu0IgNDi6hEfmBx77uHc/ioum1ys0zTHgi9/2JcYdqJ+U7o395AIYKXfk2aI56WJLiUhR2GzDyIYu992fhaqfO0ptuG9EjoJscuIVURh6sStqnJHny0fLv0zhtBLNlaVdFUY3stdRiZAb1H58UwNmG+DpQB/sJjeek61ZhQ57KjXTcq4aGCIoIZPUx51CzdIm+lzPOkrFZzNiPsIcDe27xbK3h8zISzAele+p/nk5C/d7hyjt7touYOlOKhZgGmLHR4hXTmznY/7R5lGZqstuCQSfR6DNH/M094JSrhMvaAN5tq+LUbr8eSRBd1dIw8yVaxy7QjpReM/mn26O8= 1347649631@qq.com
  
  // 将公钥加入github网站
  setting -> SSH and GPG keys
  
  // 创建初始化仓库
  git init
  // 验证连接成功
  ssh -T git@github.com
  
  操作：
  git clone xxxxx
  切换分支
  sudo git checkout --track origin/master
  
  
  ```

* 配置ssh

  ```
  sudo apt-get install openssh-server
  用户名yuqiao@192.168.88.130
  password 123456
  ```

* 配置远程vscode

  ```
  安装c/c++插件
  ```

* 配置中文

  setting->language->app

* 配置acconda

  1、上传下载好的Anaconda的sh包（/home/yuqiao/soft）

  [Index of /anaconda/archive/ | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/?C=M&O=D)

  2、解压安装

  ```
  sh Anaconda3-2022.10-Linux-x86_64.sh 
  空格--yes--定义路径--yes---安装完成---重新启动终端启动初始化
  ```

  3、配置环境变量

  ```
  vi ~/.bashrc
  export PATH=$PATH:/home/yuqiao/soft/anaconda3/bin
  
  :wq
  
  // 执行
  source ~/.bashrc
  
  // 执行完毕出现base
  ```

  4、创建python环境

  ```
  // 查看环境
  conda info --env
  // 创建环境
  conda create --name pynlp python=3.10
  // 更新环境
  source activate
  // 进入
  conda activate pytorch
  python
  // 退出
  conda deactivate 
  // 查看环境
  conda list
  ```

  5. 免密登录

  ```c
  // windows
  本地电脑打开CMD窗口，输入ssh-keygen -t rsa -C "这里任意输入"命令后默认回车生成RSA密钥对
  在本地电脑的C:\Users\[user_name]\.ssh文件夹下可以查看到刚生成的RSA密钥对
  ```

  ![image-20231020113056050](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231020113056050.png)

  ```c
  // linux
  在家（~）目录下创建.ssh目录（若存在，则忽略这一步）
  将本地电脑生成的公钥文件id_rsa.pub通过xftp或者lrzsz上传到服务器的家目录 ~/.ssh
   1    cd ~/.ssh
  2     cat ~/id_rsa.pub > ./.ssh/authorized_keys
  将公钥文件信息写入authorized_keys文件（cat命令使用>符号时，若文件不存在会自动创建。>代表覆盖，>>代表追加）
      
  执行service sshd restart或者sudo service sshd restart重启sshd服务（如果服务器版本过高可能会要求使用systemctl restart sshd）
  同时，由于ssh不希望home目录以及~/.ssh目录对组有写权限，所以需要对目录进行权限更改。同时，ssh对于authorized_keys也有权限需求。
  
  ```

  ![image-20231020113147526](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231020113147526.png)

  <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231020113211866.png" alt="image-20231020113211866" style="zoom:50%;" />

* 安装mysql

```cpp
//1
打开终端，输入以下命令以更新软件源列表：
sudo apt-get update

安装MySQL服务器：
sudo apt-get install mysql-server

安装完成后，运行以下命令以启动MySQL服务：
sudo systemctl start mysql

如果需要MySQL服务在系统启动时自动启动，可以运行以下命令：
sudo systemctl enable mysql

可以使用以下命令检查MySQL服务是否正在运行：
sudo systemctl status mysql

root用户登录服务端
sudo mysql -u root
    
============================================
如果需要在Ubuntu中使用MySQL客户端，可以运行以下命令安装：
sudo apt-get install mysql-client

安装完成后，可以使用以下命令连接到MySQL服务器：
mysql -u root -p 密码随意 123456

输入MySQL管理员密码，即可成功连接到MySQL服务器。
    
// 允许远程访问
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
删除 bind 127.0.0.1
   

// 创建其他用户
// 建立yourdb库
create database yourdb;
// 创建user表
USE yourdb;
CREATE TABLE user(
username char(50) NULL,
passwd char(50) NULL
)ENGINE=InnoDB;
// 添加数据
INSERT INTO user(username, passwd) VALUES('zhang', '123456');

// 3
sudo apt-get install libmysqlclient-dev
可以在远程连接
#include <mysql/mysql.h>
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
FLUSH PRIVILEGES;
SELECT user, host FROM mysql.user;

```











# 5 windows下aconda换源



  <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231023161438258.png" alt="image-20231023161438258" style="zoom:50%;" />

* 换回默认源

```
conda config --remove-key channels
```



