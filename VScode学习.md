## vscode命令

* ctrl+shift+p 输入命令

* 将代码分享出  carbon
* 字体 [JetBrains Mono：面向开发人员的免费开源字体|JetBrains：面向专业人士和团队的开发人员工具](https://www.jetbrains.com/lp/mono/)

​      下载后 进入 ttf目录全选安装

* ctrl+x 删除整行
* ctrl +b 隐藏目录
* alt+up/down 移动一行
* shift+alt+up 复制当前行
* ctrl+alt+up   选中多行同时操作
* ctrl+/ 单行注释
* shift+alt A 多行注释
* ctrl+~ 打开终端
* ctrl+p 调出设置窗口
* 
* alt+鼠标左键 多光标同时操作
* 移动当前行 alt+箭头
* 代码格式化 shift+alt f
* 

, Consolas, 'Courier New', monospace

## emmet语法

```
创建html文件后输入 ！ 加载默认网页代码
```

```html
ctrl + x 注释
 <!-- 注释 -->
```

```html
# 1.快速创建标签
<!-- nav>ul>li -->
    <nav>
        <ul>
            <li></li>
        </ul>
    </nav>

<!-- div>ul>li*6 -->
    <div>
        <ul>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
        </ul>
    </div>

# 2.增加标签class属性
<!-- div.hello>ul>li{item $}*5 -->
    <div class="hello">
        <rl>
            <li>item 2</li>
            <li>item 2</li>
            <li>item 2</li>
            <li>item 2</li>
            <li>item 2</li>
        </rl>
    </div>
 
# 3.id标签
<!-- #header -->
    <div id="header"></div>
<!-- wo#zhang -->
    <wo id="zhang"></wo>
```



## 语法

```py
快速生成代码框架  ！+table
```

# .vscode文件配置

## 1 c_cpp_properties.json

配置编译器路径

```json
{
    "configurations": [
        {
            "name": "Linux",        
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "compilerPath": "D:\\VScode\\mingw64\\bin\\g++.exe",  //编译器路径
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "clang-x64"
        }
    ],
    "version": 4
}

```

## 2 tasts.json

如果要构建应用程序，则需要生成`tasks.json`文件：

Ctrl+Shift+P -> Tasks: Configure Tasks… ->g++生成可活动文件

```json
```

## 3 lauch.json

debug时候需要调试文件

点击菜单栏DEBUG->Add [Configuration](https://so.csdn.net/so/search?q=Configuration&spm=1001.2101.3001.7020) ->选择C++ (GDB/LLDB)(Windows下选择C++ Windows) 



#  vscode+ssh配置git

## 1 配置用户名和邮箱

```cpp
root@iZf8zc70s5qi0tygukf9wdZ:/home/zhang/mygit/git-test# git config user.name "ZhangYuQiao326"
root@iZf8zc70s5qi0tygukf9wdZ:/home/zhang/mygit/git-test# git config user.email "1347649631@qq.com"
```

## 2 界面

### 2.1 分支

* 切换分支-----  切换后拉取以下，同步远程与本地的分支数据

![image-20230607151414656](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230607151414656.png)





* 合并分支-----由master分支触发，合并其他分支

  ![image-20230607151522119](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230607151522119.png)

### 2.2 git流程

* 分支的作用：多人协同，各自分支，最后merge到master上，然后push到云端



![image-20230607153209525](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230607153209525.png)

![image-20230607152012803](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230607152012803.png)

## 3 clone

法1

* 在指定文件夹下 cmd!(C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230607154031171.png)

* 使用命令`git clone xx`

  ![image-20230607154418383](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230607154418383.png)

* 用vscode打开，默认在master分支
* 创建自己的分支

法2

* ctrl+shift+p  命令  --> git clone
* 选择本地库位置
* 打开

## 4 在github创建项目

1、github创建项目库

2、vs git clone 项目库到本地文件夹、创建分支

3、在分支下创建文件夹、文件、代码等

4、提交到本地分支库，可以合并master和branch

5、将分支、文件一同上传到github



### 5 将本地项目上传到github

1、github创建库

2、本地项目文件下

`git push https://xxx  master`



# vs打断点

F9 ： 打断点/删除断点

Ctrl+Shift+F9: 删除全部断点

Ctrl+shift+B：生成解决方案

F5：运行代码到断点处

ctrl+F5：忽略断点运行代码

shift+F5：结束运行

F10：单步调试，不进入函数

F11：单步调试，进入函数

Ctrl+F10：运行代码到光标所在处

shift+F11：跳出当前所在的函数

Ctrl+alt+m+1：查看内存，输入地址即可看到地址下存的变量的值

F12：查看当前光标所在处的函数/变量的定义

shift+F12：转到引用









VS

ctrl alt a 打开项目文件

# 远程服务器功能说明

* region2 : autodl服务器， 含有c++, yolov5 opencv、tenserRT等库
* ubuntu22：含有anaconda

```cpp
用户名yuqiao@192.168.88.130
password 123456
```



# vscode打开html操作

1. 文件 - 打开文件夹 - 创建工作目录

2. 新建文件，若是html，则可以用 live server实时修改

   

# vs配置三方库

1. 配置头文件

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240307170336105.png" alt="image-20240307170336105" style="zoom:67%;" />

2. 配置lib库文件

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240307170354545.png" alt="image-20240307170354545" style="zoom:67%;" />

3. 配置具体的库名libmysql.lib

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240307170408447.png" alt="image-20240307170408447" style="zoom:67%;" />

4. 将ddl动态链接库放入项目执行目录下

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240307170719674.png" alt="image-20240307170719674" style="zoom:67%;" />
