激活永久专业版

[intellij idea注册码,idea激活码,idea破解教程,idea破解插件,idea注册码在线生成 (javatiku.cn)](http://idea.javatiku.cn/)

激活码 1567

# 1 创建项目

![image-20220818162927093](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220818162927093.png)

1. 可以使用虚拟环境创建项目，虚拟环境用Virtualenv创建，存放位置如图

2. 基础解释器：用于复制虚拟解释器的原本，即本机安装的python.exe

3. 虚拟环境创建的项目包括venv 文件，用于存放虚拟环境的配置

4. 注意创建时候，需要在文件项目上右键创建python文件，而不是在venv上创建

5. 用conda创建虚拟环境(本地python.exe 在tools目录，linux在bin目录)

   ```py
   # 打开conda prompt，创建虚拟环境
   conda create -n to_pack python=3.7
   
   # pycharm中新建conda解释器，选择创建的环境中的python.exe
   
   ## 注意：
   # 一个项目只能用一个虚拟环境
   # 其余项目再次使用该虚拟环境时，会默认将解释器（2）
   # 但是改变某一个解释器，会改变整体的解释器
   ```

6. 使用环境变量时候的pip问题

```py
# 默认终端的pip会下载到anaconda的本地解释器中
# 下载到虚拟环境中
1. anaconda prompt中切换到虚拟环境pip下载

2. pycharm中通过设置-项目-解释器 下载
```

7、切换远程终端

```
工具->ssh会话->选择远程服务器终端
```





![image-20230314204316001](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230314204316001.png)

* 快捷键
* ctrl+/   快速注释



文件重命名

* shift + F6



代码规范

* def定义函数时候，距离上下各两行
* 在（）外使用等号=时候，左右空一个，在（）内使用，等号=紧连前后
* class定义类名首字母大写
* 使用逗号，隔开，紧挨着前一位，空格，下一位

# 引用地址

## 绝对地址

```py
# linux
path = "/pycharm_tmp/TorchLearn/hymenoptera_data/train/ants/0013035.jpg"

# windows
path = "D:\\pycharm专业版\\project\\TorchLearn\\hymenoptera_data\\train\\ants\\0013035.jpg"
```

## 相对地址

```py
# linux, windows
path = "../hymenoptera_data/train/ants/0013035.jpg"

# 控制台中 （可直接复制文件路径）
path = "hymenoptera_data/train/ants/0013035.jpg"
```

