# python





# 一 idel 快捷键

* 上一句代码 Alt + P
* 更改字体大小格式 Option - configure idle
* 强制结束循环 Ctrl + C



# 二 基础概要（类型 结构 运算符）

## 2.1 字符串类型

* 三种表示 单引号/双引号/三引号

  ’单引号’   一般使用

  ‘’ 双引号‘’    字符串内含有引号时使用

  ‘’‘ 三引号 ’‘’   字符串需要多行输入时候使用，以上两种换行输入，需要输入\转行

  ------------

* 转义字符 \

  \在末尾，可以换行输入，在中间表示转义字符

  ==注意转义字符写在引号内==

  ==转义字符不可被打印==

  -------

* 原始字符串r 

   r之后的是一个字符串，不再是一个转义字符

  ```py
  print(r'hello,world')
  ```
  
* 打印字符串与数字

   1. ```py
      #全部转化为字符串,用+连接
      print('1'+'+'+'2'+'='+'3')    1+2=3    #固定搭配
      或者
      print(str(i)+'+'+str(j)+'='+str(i+j))  #变量ij
      ```

   2. ```py
      #数字和字符串用逗号隔开
      print(1,'+',2,'=',3)
      print('数字是',1)
      ```

-----------

## 2.2 数值类型

* int   

* float

   浮点数在计算机中的存储形式有误差，想要精确的表示需要用函数表示

  ```py
  import decmial
  a = decmial.Decmial('0.3')
  #以字符串表示精确数字
  ```

------------------

## 2.3 bool类型

只有 true （1）， false（0）

![image-20220722010226553](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220722010226553.png)


## 2.4 分支循环

### 分支

* 基本语法

  ```py
  if ---- :
      sta(s)
  else:
      sta(s)
  ```

  ```py
  if --- :
      stat(s)
  elif --- :
      sta(s)
  else:
      sta(s)
  ```

* 条件表达式

  ```py
  #if --else --表达更简洁，将代码块变为语句
  x = sta(a) if --- else sta(b)
  print(x)
  ```
  
  ```py
  #eg:
  grade = 'A' if score < 90 else 'B'
  print(grade)
  ```

### 循环

* while

  ```py
  while --- :    
  ```

* 强制跳出循环

  ```py
  break #1 跳出整个循环体，继续执行下面代码
        #2 若有循环嵌套，只能跳出一个循环体，继续执行外层循环
  continue # 跳出本次循环，但未跳出循环体，重新开始循环
  ```
  
* for

  ```py
  for i in []:    #[]内为一个可迭代的对象
      print(i)
  ```

  ```py
  #for常搭配range函数使用
  range(i,j)               #i~j-1，一共j-i个数
  for i in range(11):      #i取 0~10
  for i in range(2,11):    #i取 2~10
  for i in range(2,11,2):  #每次加2，i取2，4，6，8，10
  ```
  
* 练习

  ```py
  #查找10以内的素数,1除外
  for i in range(2,11):          #23456789 10   假设i=6
      for j in range(2,i):       #j=2345
          if i%j == 0: 
              print(i,'=',j,'*',i/j)
              break              #不跳出循环，6继续除以345
      else:                      #注意else在外层，即所有的除数都除完后，仍没有找到倍数，返回这是一个素数
              print(i,'是一个素数')
              
  ```

  
-----


-------

## 2.5 逻辑运算符

* and   or  

​     遵循短路逻辑运算，即==返回决定最终结果的一项==

```py
1 and 2    2 #1为true，最后结果由2决定，返回2
0 and 3    0 #0为false，直接得到结果
1 or 3     1 #1为true，直接得到结果
0 or 3     3 #0为false，最后结果由3决定，返回3
```

---------

* 运算符优先级（优先级越大，等级越高）
==加减乘除 -> 大小等于比较运算符 -> not -> and -> or==
![image-20220722141214994](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220722141214994.png)

# 三 序列（列表 元组 字符串）

## 3.1一维列表（数组）

* 列表内能集合各种类型，用逗号隔开

  ```py
  #创建一个列表，元素下标从0开始
  list = [1,2,3,'a','b','c']
  ```

* 下标查找/切片方式

  ```py
  list[i:j:k]   #从下标为i的元素(从第i个元素后)开始查找，直到第j个元素（下表为j-1），每次下标的增量为k,
  # 左闭右开[i,k)
  ```

  ``` py
  list = [1,2,3,'a','b','c']
  list[3]      #a   显示下标为3的元素
  list[:]      #显示全部
  list[::2]    #1 3 b
  list[2:5:2]  #3  b
  list[::-1]   # 倒序，从后到前，下表依次为-1，-2...
  [-1]：获取最后一个元素，类似于matlab中的end；
  [:-1]：除了最后一个元素，获取其他所有的元素；
  [::-1]：对第一个到最后一个元素进行倒序之后取出；
  [n::-1]：对第一个到第n个元素进行倒序后取出。
  ```

* 增（1个or多个）

  ```py
  #增加单个元素
  list.append('a')  
  
  #增加多个元素
  list.extend(['d',4])
  
  #采用切片方式,注意冒号
  list[ len(list):]='a'
  list[ len(list):]=['a',4]
  
  #指定下标位置添加单个元素，多个需要重复添加
  list.insert(i,'f')    #i 为下标
  ```

* 删（指定位置or指定元素）

  ```py
  #删掉指定元素，若有多个，删除第一个
  list.remove('a')
  
  #删除指定位置元素，i为下标
  list.pop(2)
  
  #清空列表
  list.clear()
  ```

* 改

  ```py
  #替换‘a’元素，直接指明下标
  1. list[2]='m'
  2. list[list.index('a')] = 'm'
  
  #替换多个连续元素
  list[2:] = [6,8,9]
  
  #排序
  list.sort()  由小到大 sort(reverse) reverse默认为false
  list.sort(key=len ,reverse=True)  若为数字，由大到小，原地逆置,若为字符串，按照长度排序
  
  #逆置
  list.reverse()
  ```

* 查

  ```py
  #查询某元素出现的次数
  list.count('a')
  
  #查询某元素的索引值/下标
  list.index('a')
  
  #一维列表浅拷贝，即拷贝件不再受原件影响
  1. num = list.copy()
  2. num = list[:]
  ```

​        ==拷贝==：复制文件不受原文件影响，分为浅拷贝和深拷贝

​        ==引用==：复制文件指向原文件，受原文件的影响

## 3.2 二维列表（嵌套列表）Matrix

* 创建

  ```py
  #先创建一行三列
  A = [0] * 3                 [0,0,0]
  #往每一列填充嵌套
  for i in rage(3):
      A[i] = [0] * 3          [[0,0,0],[0,0,0],[0,0,0]]
  ```

  ```py
  #列表推导式创建嵌套列表
  A = [[0]*3 for i in range(3)]
  ```

  

* 改

  ```py
  A = [[1,2,3],[4,5,6],[7,8,9]]
  import copy
  B = copy.copy(A)     #多维数组的浅拷贝，拷贝的是路径，受原文件影响，不完全拷贝
  C = copy.deepcopy(A) #深拷贝，完全拷贝，不受原文件影响
  ```

  ```PY
  一维列表      多维列表
  
    引用   ===  浅拷贝 
  
    浅拷贝  ===  深拷贝
  ```

* 切片

  ```py
  #coding=utf-8
  import numpy as np
  a=np.arange(12)
  print(a)
  #结果：[ 0  1  2  3  4  5  6  7  8  9 10 11]
  
  #reshape对一维数组进行修改形状 (4,3)修改为4行3列
  a=a.reshape((4,3))
  print(a)
  #结果：
  [[ 0  1  2]
   [ 3  4  5]
   [ 6  7  8]
   [ 9 10 11]]
  
  二维索引的使用
  #索引的使用，获取第三行
  print(a[2])
  #结果：[ 6  7  8]
  
  #索引的使用，获取第二行第三列
  # a[1][2] = a[1,2]
  print(a[1][2])
  #结果：5
  ```

  ```
  #切片的使用，[行进行切片,列进行切片] 即[start:stop:step,start:stop:step]
  #获取所有行
  print(a[:,:])
  #结果：
  [[ 0  1  2]
   [ 3  4  5]
   [ 6  7  8]
   [ 9 10 11]]
  
  
  
  #获取所有行，部分列 {所有行，第二列}
  print(a[:,1])
  #结果：[ 1  4  7 10]
  
  #获取所有行，部分列 {所有行，第一、二列}
  print(a[:,0:2])
  #结果：
  [[ 0  1]
   [ 3  4]
   [ 6  7]
   [ 9 10]]
   
  
  
  #获取所有列，部分行 {所有列，获取行的步长为2}
  print(a[::2,:])
  #结果：
  [[0 1 2]
   [6 7 8]]
   
  #获取所有列，部分行 {所有列，获取行第一、第二行}
  print(a[0:2,:])
  #结果：
  [[0 1 2]
   [3 4 5]]
  
  
  
  #获取部分行，部分列 {行的奇数行，列的第一、第二列}
  print(a[::2,0:2])
  #结果：
  [[0 1]
   [6 7]]
  ```

  

## 3.3 列表推导式

```py
#一般形式
#先for i 建立[, , , ],再对每一项进行操作
A = [结果形式 for i1 in 迭代对象 if condition1]

#完整形式（含有嵌套）
A = [结果形式 for i1 in 迭代对象1 if condition1
             for i2 in 迭代对象2 if condition2
              ...
                                            ]
#创建1-100 序号的数组
list = [i for i in range(1,101)]
```

```py
#一维列表

#A = [1,2,3],每个数扩大两倍
B = [i*2 for i in A]     [2,4,6]
#
B = [str*2 for i in 'hello']    ['hh','ee','ll','ll','o']
```

```py
#二位列表
A = [[1,2,3],
     [4,5,6],
     [7,8,9]]
#获取第二列的数字
B = [row[1] for row in A]    #先获取每一行给row，再获取每行的第二个数
#获取对角线数字
B = [A[i][i] for i in range(len(A))]   B=[1,5,9]
#获取反对角线数字
B = [A[i][len(A)-1-i] for i in range(len(A))]   B = [3,5,7]
#二维展开为一维
B = [j for i in A for j in i]
```

```py
#嵌套
A = [[x,y] for x in range(10) if x%2==0
           for y in range(10) if y%2==0]

```

## 3.4 元组 tuple

* ==元组不可修改==，只有==查==

  若元组里面有列表，

* 用（）或者不要括号表示，用逗号隔开

* 列表的查操作同样适用于元组

  ```py
  a = (1,2,3)
  b = ('q','w')
  a+b = (1,2,3,'q','w')
  a*2 = (1,2,3,1,2,3)
  c = [a,b]    c=[(1,2,3),('q','w')]
  ```

* 生成一个元素的元组

  ```py
  a = (10)
  type(a)=int
  b = (10,)
  type(b)=tuple
  ```

* 打包和解包（生成元组，多重赋值）

  ```py
  #打包
  a = (1,2,3)
  #解包
  x,y,z = a
  x=1
  y=2
  z=3
  ```

  ==python特有技能，多重赋值==

  ```py
  x,y = 1,2
  ```

* 元组推导式 = 生成器表达式 见6.7

  将列表推导式中括号换成圆括号，生成生成器（迭代器）

## 3.5 字符串

* 大小写

  ```py
  a = 'i love you'
  #首字母变成大写
  a.title()
  #所有字母大小写反转
  a.swapcase()
  #所有字母大写
  a.upper()
  #所有字母小写
  a.lower() #只能处理英文
  a.casefole() #还可以处理其他字符
  
  ```

* 填充字符左中右对齐

  ```py
  a = '123'
  #居中
  a.center(5,'@')  @123@   #两个参数，第一个是最后的字符数，第二个是填充字符，默认为空
  #左对齐
  a.ljust(5) '123  '
  #右对齐
  a.rjust(5) '  123'
  #用0填充，左对齐
  a.zfill(5) '000123'
  ```
  
* 查找

  ```py
  a = '我要去大海看海龟'
  #查找出现次数
  a.count('海') 2 #三个参数，1查找项 2起始下标 3查找字符串个数，默认范围为全部
  #查找出现的第一个下标
  a.find('海') 4 
  a.find('海'，0，5) 4
  #从右往左查
  a.rfind('海') 6
  #find和index功能一样，区别在于find找不到返回-1，index找不到报错
  a.index('海') 4
  ```
  
* 替换

  ```py
  a = '在吗，i love you'
  a.replace('在吗'，'想你')   a = '想你，i love you'
  #按照固定模式替换
  #先制作固定模式 ，三个参数 1代替换的元素 2 替换成的新元素 3 遇到直接省区的元素，默认为空
  table = str.maketrans('abcdefg','1234567','在you')
  #使用translate替换
  a.translate(table)  a = '吗，i lv5 '
  
  ```

* 判断布尔类型

  ```py
  #判断开头结尾是否为某元素，三个参数 1待判断元素 2开始下标 3判断的元素个数，默认范围全部
  a = '我爱python'
  a.startswith('我') 
  a.endswith('n')
  #判断是否是首字母大写
  a.istitle()
  #判断所有字符是否为大/小写
  a.isupper()
  a.islower()
  #判断字符串内是否为字符（汉字或者英文，不包括数字）
  a.isalpha()
  #判断字符串内是否为数字
  a.isdecimal() #识别阿拉伯数字
  a.isdight() #识别阿拉伯，罗马数字
  a.isnumeric() #识别阿拉伯，罗马，汉字数字
  #判断字符串是否为空  (包括 1空格 2tab 3\n)
  a.isspace()
  #判断是否可打印（\转义字符不可打印）
  a.isprintable()
  #判断是否为合法标识符  #英文，下划线开头
  a.isidentifier()
  
  ```

* 截取字符串

  ```py
  #两侧侧删除某匹配的字符串，直到下一个字符不匹配，默认删除空格
  a.lstrip()
  a.rstrip()
  #删除两侧具体字符串
  a.removeprefix()
  a.removesuffix()
  ```

* 分割字符串

  ```py
  #按照特定字符分割字符串，只能分割一次，输出为元组，三个部分
  a = '苟日新，日日新，又日新'
  a.partition('，')  ('苟日新', '，', '日日新，又日新')
  a.rpartition('，') ('苟日新，日日新', '，', '又日新')
  
  #按照特定字符分割，可以分割多次成列表，两个参数，1 分割元素  2 分割次数，默认为none，即全部切割
  a.split('，')   ['苟日新','日日新', '又日新']
  a.split('，'，1)  ['苟日新','日日新, 又日新']
  
  #根据换行符全部划分，默认参数为  是否保留换行符
  '苟日新\n日日新\n又日新'.splitlines()    ['苟日新', '日日新', '又日新']
  '苟日新\n日日新\n又日新'.splitlines(True)  ['苟日新\n', '日日新\n', '又日新']
  ```

* 连接字符串

  ```py
  # 1 加号连接
  '我是'+'一个兵'    '我是一个兵'
  #2 join函数连接  参数可以指定通过任意字符连接, 待连接的字符串以列表形式表示
  ''.join( ['我是'，'一个兵'] )   '我是一个兵'
  '@'.join([ '你','好'])  '你@好'
  ```
## 3.6 格式化字符串/f-string

* 变量字符串格式化

  ```py
  # 顺序定位
      '我是{}，我来自{}，我今年{}岁'.format('张艺谋','中国',22)
    
      #索引定位 0开始
      '我来自{1}，我是{0}，我今年{2}岁'.format('张艺谋','中国',22)
    
      #关键字定位
      '我来自{place}，我是{name}，我今年{age}岁'.format(name='张艺谋',place='中国',age=22)
  ```

* 数字字符填充对齐填充 =={:<}==

  ```py
  '{1:$<10}'.format('a','b')
  1 索引第二个字符
  ：开始参数
  $ 填充字符
  < 对齐方向
  10 填充长度
  ```

  ```py
  #用0填充时候，用=可以强制将符号提到前面
  '{0:0<10}'.format(123)
  '1230000000'
  '{0:0>10}'.format(123)
  '0000000123'
  '{0:0<10}'.format(123)
  '1230000000'
  '{0:0=10}'.format(123)
  '0000000123'
  '{0:0=10}'.format(-123)
  '-000000123'
  '{0:010}'.format(-123)
  '-000000123'
  '{0:0>10}'.format(-123)
  '000000-123'
  
  ```

* 浮点数精度表示=={:.}==

  ```py
  '{:.2f}'.format(123.128)    #四舍五入保留小数点后两位  123.13
  '{:.4g}'.format(123.123)    #四舍五入，总共保留四位  123.1
  '{:.4}'.format('zhangyuqiao')  zhan
  
  #百分号
  '{:.2%}'.format(0.1234)  '12.34%'
  
  #千分号
  '{:，}'.format(1234567)   '1,234,567'
  ```

* 进制转换=={:#}==

  ```py
  加上#输出可以自带进制前缀
  '{:#d}'.format() 10进制
  '{:#b}'.format() 2进制
  '{:#o}'.format() 8进制
  '{:#x}'.format() 16进制
  ```

  ```py
  '{:#d}'.format(0b1011)
  '11'
  '{:#o}'.format(0b1011)
  '0o13'
  '{:#b}'.format(0x1011)
  '0b1000000010001'
  ```

* f-string / f字符串

  

  ```py
  name = '张雨乔'
  age = 12
  # 对于变量，可以直接引用
  f'我的名字是{name}，年龄是{age}'
  '我的名字是张雨乔，年龄是12'
  
  #对于数字的格式化，去掉format，在  ''  前加上f，具体数字写在：前，其余格式不变
  f'这个字符串{123.12345:.5g}'
  '这个字符串123.12'
  
  f'格式化后{-234:%<8}'
  '格式化后-234%%%%'
  
  f'进制转换{0x43:#b}'
  '进制转换0b1000011'
  
  ```

## 3.6 迭代器和迭代对象

* 区别：迭代器是一次性的，迭代对象可以不断操作

* 迭代器返回的是0X十六进制数字，需要用list以列表返回

* 常见的返回值为迭代器的函数 reversed，enumerate， zip，map， filter

* 迭代对象转为迭代器

  ```py
  iter()
  a = [1,2,3,4]
  b = iter(a)
  
  #next函数可以逐一输出迭代器的值，返回结束后报错，可以加入第二参数调整
  next()
  next(b)
  1
  next(b)
  2
  next(b)
  3
  next(b)
  Traceback (most recent call last):
    File "<pyshell#9>", line 1, in <module>
      next(b)
  StopIteration
  next(b,'第二参数调整——返回结束')
  '第二参数调整——返回结束'
  
  #也可以用循环表达式一次性访问生成所有的迭代器
  for i in b:
      print(i)
      
  ```
  
  

## 3.7 作用于序列的运算符和函数

* 列表是 ==可变==序列，相同的序列有==不同==id，更改本序列时，id保持==不变==

* 元组，字符串是==不可变序列==，相同的序列有==相同==id，更改本序列时，id==变化==

* 对象

  ```py
  #判断id是否相同(是否为同一对象)
  a = [1,2]
  b = [1,2]
  a is b  False
  
  a = '1,2,3'
  b = '1,2,3'
  a is b  True
  
  #判断某元素是否在序列内
  1 in [1,2,3] true
  '1' in ['1','2'] true
  
  #删除序列
  a = '1,2,3'
  del a
  
  #删除系列内某元素
  a = [1,2,3,4,5]
  del a[::2]  [2,4]
  ```

* 相互转换

  ```py
  list[]
  tuple[]
  str[]
  ```

* 排序逆置

  ```py
  #求序列内最大最小值，两个参数 1 序列 2 若查找失败，返回default
  min(a,default=  ) 
  a = [1,2,3]
  min(a,default='啊啥都没有！')
  
  #求序列内容长度
  len()
  
  #若序列内为数字，求和  两个参数，start从100开始求和
  sum（a,start=100）
  
  #若为序列内为数字，排序（三个参数 key/reverse）
  a.sort()   列表专用，改变a本身
  sorted()   列表，元组，字符串都能用，重新申请一个值，a本身不变
  
  #逆置
  a.reverse() 列表专用
  reversed()  序列均可使用，返回的是==反向迭代器== 用list转化为列表
  list(reversed(a)) == a.recversed()
  ```
* 枚举函数
  ```py 
  #将可迭代对象中的每个元素 与 从0开始的序号 共同组成二元组列表
  emp = ['a','b','c','d']
  enumerate(emp,10) 从10开始，默认0   <enumerate object at 0x000002AF4310CDC0>
  list(enumerate(emp)) 
  [(10，'a'),(11,'b'),(12,'c'),(13,'d')]
  #返回二元组
  ```


* 拉链函数

  ```py
  #将多个可迭代元素中的元素，拼接在一起，构成一个多元组列表
  a = [1,2,3]
  b = [4,5,6]
  c = 'abcde'
  zip(a,b,c)     <zip object at 0x000002AF430BD3C0>
  list(zip(a,b,c))   
  [(1, 4, a), (2, 5, b), (3, 6, c)]
  #返回最小长度匹配的元素，其余元素舍弃
  
  #解决舍弃问题
  import itertools
  itertools.zip_longest(a,b,c)  <itertools.zip_longest object at 0x000002AF431A6D40>
  list(itertools.zip_longest(a,b,c))
  [(1, 4, 'a'), (2, 5, 'b'), (3, 6, 'c'), (None, None, 'd'), (None, None, 'e'), (None, None, 'f')]
  ```

* map函数

  ```py
  #根据所提供的函数对提供的可迭代对象进行运算
  map(A函数，[对象1]，[对象2]，[对象3])   A函数有三个参数
  eg：
  map(ord,'zhang')  ord函数只有1个参数
  list(map(ord,'zhang'))  [122, 104, 97, 110, 103] 
  
  map(max,[1,2,3],[4,5,6],[7,8,9])  
  list(map(max,[1,2,3],[4,5,6],[7,8,9]))  [7, 8, 9]
  #分别从[1,4,9]  [2,5,8],[3,6,9]中找出最大值
  ```

* 过滤函数

  ```py
  #可以筛选出满足条件的元素
  filter(str.islower,'ABCacb')  返回小写
  list(filter(str.islower,'ABCacb') )
  ['a','b','c']
  ```


# 四 字典--映射

## 4.1 增删改查

```py
#6种方法
a = {'刘备':'留意的','关羽':'关云长','张飞':'张翼德'}

b = dict(刘备='留意的'，关羽='关云长',张飞='张翼德') //只有此方式，键不用冒号

c = dict({'刘备'：'留意的'，'关羽':'关云长','张飞':'张翼德'})

d = dict(('刘备','留意的')，('关羽','关云长'),('张飞','张翼德'))

e = dict({'刘备'：'留意的'，'关羽':'关云长'},张飞='张翼德')

f = dict(zip(['刘备','关羽','张飞'],['留意的','关云长','张翼德']))

{'刘备': '留意的', '关羽': '关云长', '张飞': '张翼德'}

#快速创建值相同的字典
a = dict.fromkeys('fish',250)
{'f': 250, 'i': 250, 's': 250, 'h': 250}
```

* 删除元素

  ```py
  #删除键，来删除元素
  a.pop('刘备')
  #删除不存在的元素，返回规定值
  a.pop['赵云','不存在']
  #法2
  del a['关羽']
  
  #清除为空字典
  a.clear()
  ```

* 插入元素/更改元素值

  ```py
  a['赵云'] = '赵子龙'
  
  a['刘备'] = '刘玄德'
  
  #同时插入多个元素
  a.update(马化腾='腾讯',马云='爸爸')
  # 直接插入一个新的字典
  b = {'宋江':'sj','李逵':'黑旋风'}
  a.update(b)
  ```

* 查

  ```py
  #键索引，不存在返回错误
  a['张飞']
  
  #get查询，不存在返回指定值
  a.get('白起','不存在')
  
  #setdefault查询，不存在返回指定值，并将指定值插入字典中
  a.setdefault('白起','战神')
  ```

* 引用和拷贝

  ```py
  #引用
  b = a
  #拷贝
  b = a.copy()
  ```

  

## 4.2字典视图对象

* 键值对 items   a.items()获取

* 键 keys    a.keys()获取

* 值 values  a.values()获取

* 字典视图对象的值，随着字典的变化而变化（特性）

  ```py
  #查询键值对的个数
  len(a)
  
  #查询某键是否在字典内
  '刘邦' in a
  
  #将键/值转为列表
  list(a)
  list(a.values)
  
  #将键转为迭代器
  b = iter(a)
  next(a)
  ```

## 4.3 嵌套

```py
#字典嵌套字典
a = {'刘备':{'刘玄德'：1}, '关羽': {'关云长'：2}, '张飞':{'张翼德：3'}}
a['关羽'][关云长] 2

#字典嵌套列表
a = {'刘备':[1,2,3], '关羽': [4,5,6], '张飞':[7,8,9]}
a['关羽'][2]  6
```

## 4.4 字典推导式

```py
#调换键与值
b = {y:x for x,y in a.items()}

#显示ascii码
b = {x:ord(x) for x in 'abcde'}

#键值唯一，1：4，1：5，1：6覆盖
b = {x:y for x in [1,2,3] for y in [4,5,6]}
{1: 6, 2: 6, 3: 6}
```

# 五 集合 --无序唯一

* 无序， 唯一（用于去重）

## 5.1 增删改查

* 创建

```py
#直接
a = {1,2,3}
#集合推导式
a = {i for i in 'hello'}
#set
a = set('hello')

```

* 增

```py
#每个元素作为增加
a.update('abc')  a = {'1','2','3','a','b','c'}

#整体作为增加
a.add('abc')  a = {'1','2','3','abc'}
```

* 删

```py
#没有此元素显示异常
a.remove(b)

#没有元素返回原集合
a.discard(b)

#清空集合
a.clear()
```





## 5.2 查找

* 无序唯一，不能使用下标索引，只能判断是否在集合内

  ```py
  in
  not in 
  ```

* 判断是否有重合元素

  ```py
  len(a) == len(set(a))
  ```

## 5.3函数方法（函数内为可迭代参数可自动变为集合）

```py
#判断a与b是否有交集
a.isdisjoint(b)

#判断b是否是a的超集（母集）
a,issuperbset(b)
b > a

#判断a是否是b的子集
a,issubset(b)
a < b

#求ab并集
a.union(b)
a | b

#求ab交集
a.intersection(b)  
a & b

#求a与b的差集（中除了ab交集以外的元素）
a.difference(b)
a - b

#求ab中除了交集以外的元素(对称差集)
a.symmetric_difference(b)
a ^ b 脱字符
```



## 5.4 哈希函数

* 一个固定不变的元素有唯一个哈希值，即==不可变的元素==，通过hash()获取

  有哈希值：数字类型，字符串类型，元组

  没有哈希值：列表，字典，集合

* ==字典==的键，值必须要用有哈希值的元素

* ==集合==元素必须为哈希元素

  ```py
  #实现集合嵌套，将集合转换成冰雪美人集合
  a = {1,2}
  a = fromzenset(a)
  
  b = {a,'f'}
  ```

  

# 六 函数

## 6.1 定义

```py
def fuction(num1,num2):
    pass
#默认return none
#print与return的区别： 函数内有return，可以直接将函数返回值赋给新的参数
#                    函数内有print，只能打印出函数值，不能直接给新参数赋值
1位置参数：要注意形参和实参的位置顺序一致
2关键字参数：
          def myfunc(num1 = a,num2 = b):
               pass
        #注意混合使用时，位置参数放前面，关键字参数放最后
3默认参数：定义参数时可以默认参数值，放最后，传参时可以覆盖默认参数
```

==疑惑==

1. 函数传入的实参是以字符串形式传入的，所以，‘’.join() 连接字符串时候，不用加引号



```py
#限制  /   左边只能使用位置参数，右侧随意
def fun(a,/,b,c):
    pass

#限制  *  右边必须使用关键字参数
def fun(a,*,b,c):
    pass
```

## 6.2 打包和解包

* 打包--将元素以==元组==或者==字典==形式收集

* 解包--将==元组==或者==字典==内的元素分开为单个数值给函数赋值

* 普通形参--只能赋值一个实参

* 收集参数--能赋值多个实参，包括==*args--元组打包==，==**kwargs--字典打包==

  ```py
  1 #元组收集变量前面用*标注
  def myfunc(*args): #定义打印函数
      print('args')  
      
  myfunc(1,2,3,4)
  (1, 2, 3, 4)     #args为元组
  
  #解包
  a = (1,2,3，4)
  def myfunc(a,b,c,d):
      print(a,b,c,d)
     
  myfunc(*a)    #用*解包元组，给函数传参
  1，2，3，4
  ```

  ```py
  2 #字典形收集参数前用**标注
  def myfunc(**kwargs):
      print(args)
  #调用函数用关键字赋值  
  myfunc(a=1,b=2,c=3)
  {'a': 1, 'b': 2, 'c': 3}   
  
  #解包
  kwargs = {'a': 1,'b':2,'c':3}
  def myfunc(a,b,c,):
      print(a,b,c)
     
  myfunc(**kwargs)
  1 2 3
  
  ```

  ```py
  3#组合
  def myfunc(a,*b,**c):
      print(a,b,c)
      
  myfunc(1,2,3,4,5,A='a',B='b')
  1 (2, 3, 4, 5) {'A': 'a', 'B': 'b'}
  ```

## 6.3 作用域

* 局部变量--在函数内部，只能被函数访问

* 全局变量--在函数内外都可以被访问，函数内部可以覆盖全局变量值，但是外部访问不变

* global--在函数内覆盖全局变量，创建新的全局变量

  ```py
  x = 123
  def hello():
      global x
      x = 456
      
  hello()
  456
  print(x)
  456
  ```

* 函数嵌套时，nonlocal通过修改内部函数内的局部变量，来覆盖外部函数的局部变量

  ```py
  def fun1():
      x=123
      def fun2():
          x=456
          print(x)
      fun2()
      print(x)
      
  fun1()
  456
  123
  
  #修改
  def fun1():
      x=123
      def fun2():
          nonlocal x
          x=456             #修改外层函数x的值，即123变为456
          print(x)
      fun2()
      print(x)
      
  fun1()
  456
  456
  
  ```

## 6.4 闭包函数--内外层嵌套函数

* 当两个函数嵌套时候，无法直接使用内部函数，需要通过调用外部函数间接使用内部函数

* 通过闭包可以直接使用内部函数，构建闭包函数一般为==空参数==

  ```py
  def fun1():
      fun2():                    #
          print('内部函数')       # 
      return fun2                # 通过return返回，使内部函数成为闭包函数
  print('外部函数')
  
  #此时，使用fun1返回的是fun2的引用
  #直接使用fun2
  fun = fun1()  #将fun2的引用赋给fun，即fun()= fun2()
  fun()  #使用fun2（）
  ```

  

## 6.5装饰器--函数作为参数

```py
1#未使用装饰器
import time

def time_master(func):
    print('开始...')
    start = time.time()
    func()
    print('结束...')
    stop = time.time()
    print(f'共花费{(stop-start):.2f}秒。')

def sleep():
    time.sleep(2)
    print('执行完毕')

last_time = time_master(sleep)

```

```py
2#使用装饰器
import time
                    
def time_master(func):            #相当于一个叫做time_master的加工厂，让后面的材料进行加工，输出指定形式
    def call_func():              #闭包，定义加工厂里的加工步骤
        print('开始...')
        start = time.time()
        func()
        print('结束...')
        stop = time.time()
        print(f'共花费{(stop-start):.2f}秒。')
    return call_func              #闭包

@time_master()       #相当于sleep = time_master(sleep)  #连接到加工厂（工厂包括 名+内部流程）
def sleep():
    time.sleep(2)
    print('执行完毕')

sleep()   #相当于调用time_master(sleep)
#装饰器主要作用是：
```

* 给装饰器传参

```py
import time

def logger(msg):
    def time_master(func):        #两层嵌套
        def call_func():          #闭包
            print('开始...')
            start = time.time()
            func()
            print('结束...')
            stop = time.time()
            print(f'{msg}共花费{(stop-start):.2f}秒。')
        return call_func
    return time_master

#未加装饰器
def funA():
    time.sleep(2)
    print('正在调用funA')

funA = logger(msg='A')(funA)  #顺序传递两个参数，得到闭包函数
funA()

#加装饰器
@logger(msg='A')     
def funA():
    time.sleep(2)
    print('正在调用funA'）

funA()
```

## 6.6 匿名函数--lambda表达式

```py
#语法
lambda args1,args2 : expresion
```

```py
#定义squre函数
squre = lambda x: x*x
squre(2)  4
```

## 6.7 生成器

* 生成器是一种特殊的迭代器，需要用nexy()或者for i in ：访问具体内容

```py
#语法
yield num1
```

```py
#斐波那契数列
def fib():
    a,b = 0,1
    while True:
        yield a
        a,b = b, a+b
        
f = fib()
next(f)
0
next(f)
1
next(f)
1
```

* 生成器表达式

  ```py
  #列表推导式
  a = [i**2 for i in range(10)]
  a
  [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
  
  #生成器表达式/元组推导式
  a = (i**2 for i in range(10))
  a
  <generator object <genexpr> at 0x000002E14B695EE0>
  next(a)
  #用next()访问生成器（迭代器）
  next(a)
  0
  next(a)
  1
  next(a)
  4
  next(a)
  9
  next(a)
  16
  #用循环表达式继续访问
  for i in a:
      print(i)
  
      
  25
  36
  49
  64
  81
  
  ```


## 6.8 递归

* 思想：将事物分成两个情况，一个是为1的时候，将会怎么做，另一个是大于1时，将会怎么做

* 大于1的情况时，按照 2个情况计算，具体1操作用print，其余n-1看为一个整体，用==函数==实现操作

  ```py
  #汉诺塔
  def han(n,a,b,c):
      if n == 1:                #为1时
          print(a,'-',c)
      else:                     #大于1时
          han(n-1,a,c,b)
          print(a,'-',c)
          han(n-1,b,a,c)
  ```

## 6.9 函数文档

* 函数文档一定是在函数的最顶部，用引号括起来，用help查看

* 格式为：函数功能介绍，各个参数值，返回值

  ```py
  #定义一个货币兑换函数
  def doller(UN,rate=6.7):
      '''
      功能：输入美元，兑换成人民币
      UN：美元值
      rate：汇率，默认为6.7
      返回值：人民币值
      '''
      return UN * rate
     
  help(doller)
  Help on function doller in module __main__:
  
  doller(UN, rate=6.7)
      功能：输入美元，兑换成人民币
      UN：美元值
      rate：汇率，默认为6.7
      返回值：人民币值
  
  ```

## 6.10 类型注释

* 用来函数中的==参数和结果==做类型注释，用_annotations_查看类型注释

```py
def hello(x:int,y:str)->str:       #注释：x输入为int型，y为str型，返回str型
    pass

hello.__annotations__  #注意，左右均为两条_(内省函数)
{'x': <class 'int'>, 'y': <class 'str'>, 'return': <class 'str'>}

```

## 6.11 import 和 from..import..区别

```py
import x   直接导入模块x，之后调用，要用模块名.函数名，即x.fun()
from x import y   从模块x中导入函数y，之后可以直接调用函数y，不用加模块名，即y(),或者y.fun()

#一般来说，仅用模块中的函数，用import，两层嵌套
#使用模块中的函数的方法，用from..import..，三层嵌套
```



# 七 文件与目录

## 7.1 文件基本操作

* 创建/打开/保存/关闭文件

```py
open(file,mode,encoding= )
#file: 指定一个要打开已存在的文件路径；或者创建为文件名，默认创建在python主文件下

f = open('finsh.txt','w')

#不关闭文件保存
f.flush
#关闭文件自动保存
f.close()
```

![image-20220820153113276](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220820153113276.png)

* 写入操作有两种

    1 w 写入字符串（文本，代码）    

   ```py
   with open("a.txt","w"):
       f.write("abcdefg")
   ```

     2 wb  写入二进制（图片，音频等）

  ```py
  with open("video.mp4","wb"):
      f.write(requests.get(url).content)  #写入内容
  ```

* 写入(需要手动添加换行符)

```py
#单行写入
data = "hi"
f.write(data+"\n") 
data = "py"
f.write(data) 

hi
py

#连续输入多行
list = ["hi","py","like"]
for line in list:
    f.writre(line+"+")
```

```py
#多行写入，了解
list = ["hi","py","like"]
f.writelines( [line+"\n" for line in list] )#列表推导式添加换行符，重新构造列表，但是会造成最后一行多出一个换行符

#=========最常用===========
list = ["hi","py","like"]
f.writelines( "\n".join(list) )  #连接列表内的字符串，相当于传入一个字符串
```

* 注意坑

```py
f = open('finsh.txt',mode)
#当mode = ’w‘ 'r+'写状态时候，会覆盖掉之前的内容
#想继续写，用a追加写方式

#读文件r
#打开旧文件读,覆盖写，用 'w+'
#打开旧文件读,追加写，用 'a+'
```

* 读取

```py
#文件内部设有指针，通过指针指向来读取文件，通常从0开始读取

#获取指针位置
f.tell()
#更改指针位置,向前跳过n个字符
f.seek()
#=========读取全部，包括\n==============
f.read()

#读取一行,包括\n
f.readline()
#读取全部
while f:
    data = f.readline()
    print(data,end='')

#读取多行# 最常用
datas = f.readlines()    #以列表形式存储多行

for line in datas:
    print(line,end='')


```



## 7.2 路径

* 得到python.exe的位置路径

```py
from pathlib import Path  #从pathlib模块里面导入Path函数
Path.cwd()   #调用Path函数的cwd方法获取路径
WindowsPath('C:/Users/zhang/AppData/Local/Programs/Python/Python310')

p = Path.cwd
```

* 拼接文件路径

```py
#用/连接路径，Path对象/Path对象    Path对象/字符串   字符串/Path对象
q = p/ 'fish.txt'
```

* 绝对路径

  ```py
  #windows默认为两个反斜杠，一个用来转移字符
  d:\\data.txt
  d:/data.txt
  r'd:\data.txt'
  ```

  

## 7.3 路径常用操作

* 判断路径p是否为一个文件夹

```
p.is_dir()
```

* 判断路径p是否为一个文件

```
p.is_file()
```

* 判断路径p是否存在

```
p.exists()
```

* p的名字（文件名+后缀名）

```
p.name
```

* p的文件名

```
p.stem
```

* p的后缀名

```
p.suffix
```

* p的上一级

```
p.parent
```

* 将p上一级用迭代器返回

```
ps = p.parents
#<WindowsPath.parents>
#逐个访问
for i in ps:
   print(i)

D:\pycharm专业版\project\PaChong
D:\pycharm专业版\project
D:\pycharm专业版
D:\


#用数组访问
ps[1]
ps[2
```

* 将文件路径分成元组储存

```
p.parts
```

* 查询文件信息

```
p.stat()
#查询文件大小字节
p.stat().st_size
```

## 7.4 上下文管理器 with open

```py
#pycharm打开或者读取文件默认为utf-8，with open encoding默认为gbk
with open('finsg.txt','w',encoding = "utf-8") as f:
    datas = f.readlines()   
    for line in datas:
         print(line,end='')
       
    
#自动关闭文件
#将操作放在函数体内
```

## 7.5 主函数读取配置文件参数

```py
#文件名dbconfig.text
# this is a config file
user = root
password = 12345
url = www.hao123.com
```

```py
#主文件
db_dic = {}      #放配置函数参数到字典

with open("dbconfig.text", "r") as f:
    datas = f.readlines()
    #过滤注释
    for line in datas:
        if line.startswith("#"):
            continue
        #截取key和value
        key = line.split("=")[0]
        value = line.split("=")[1].replace("\n","") #去除显现出来的换行符
        db_dic[key] = value     #导入字典


if __name__ == "__main__":
    print(db_dic)

```

## 7.5 目录操作

* 创建删除

```py
#相对路径创建或删除目录
import os
import shutil
#用变量赋值，优化多次操作
dir = 'dirdemo'   #相对路径创建
dir = r'd:\dirdemo' #绝对路径创建

if not os.path.exists(dir): #如果dirdemo目录存在就删除，不纯在就创建
    os.mkdir(dir) #创立一级目录，创建多级目录：os.makedirs()
else:
    os.rmdir(dir) #只能删除相对路径下的目录
    shutil.rmtree(dir)  #权限更加强大，能删除绝对路径表示的目录
```

* 获得绝对路径

```py
print(os.getcwd())
```

## 7.6 遍历目录

* OS模块

```py
import os

os.getcwd()
os.listdir()
os.walk()
os.path.join
os.path.excits()
```



* 获得目录下的所有文件（分不出文件和目录）-----listdir()

```py
filenames = os.listdir(os.getcwd())
for name in filenames:
    print(name)
```

* 分出目录和文件，并递归遍历目录--------walk()

```py
import os

for name in os.walk(os.getcwd()): #结果为数组，逐一输出
    print(name)
   
#结果以元组形式返回，（‘目录绝对路径’，[子目录名]，[子文件名]）,并且递归遍历子目录
('D:\\pycharm专业版\\project\\python_learing\\file', [], ['conment.py', 'dbconfig.text', 'encoding.py', 'withopen.py'])

#通过元组解包，分别收纳三个信息
for dirpath, subdirs, subfiles in os.walk(os.getcwd()):
    for name in subdirs:
        print('目录',os.path.join(dirpath,name))  #组成子目录的绝对路径
    for name in subfiles:
        print('目录',os.path.join(dirpath,name))
        
#最终结果
目录 D:\pycharm专业版\project\python_learing\file\conment.py
目录 D:\pycharm专业版\project\python_learing\file\dbconfig.text
目录 D:\pycharm专业版\project\python_learing\file\encoding.py
目录 D:\pycharm专业版\project\python_learing\file\withopen.py  
```

# 八 类和对象

## 8.1 定义

1. 面向对象的方法（OOP）：将属性和函数封装到一起，向造物主一样思考，创造一个对象

2. 类：构造函数（初始化）+属性（变量）+ 方法（函数）

3. 类用 class定义，类名首字母==大写==

4. ```
   通过 c.__dict__ 来查看实例对象的属性，用字典表示
   ```

5. 类的三个特征：封装，继承，多态

6. 实例对象的三个特征：值，id，类型

   * 封装

   ```py
   class Turtle:   #定义一个乌龟类
       legs = 4
       head = 1    #定义属性
       def crawl(self):
           print('我爬得很慢！！')
       def sleep(self):          #定义属性时候，函数的默认参数为self，即判断是哪一个实例对象调用该函数，即身份证
           print('ZZZZ....')    
           
   #初始化一个对象
   red = Turtle()  #定义一个叫gui的乌龟类
   red.head
   1
   red.crawl()
   我爬得很慢！！
   ```

   * 继承：==子类拥有父类的属性和方法==，并且可以覆盖

   ```py
   #继承语法
   class Parents:
       x =1
       def hello(slef):
           print('我是父类')
           
   class Son(parents):          #继承，用括号将父类括起
       pass
   
   #当父类有初始化数据时，继承并不能直接使用
   class p:
       def __init__(self):
           print("我是p")
           
   class s(p):
       pass
   
   pp = p()
   我是p
   
   ss = s()
   #并不直接显示我是p
   ```

   * 多重继承：子类可以继承多个父类，但是多个父类共同的属性中，继承第一个继承的父类属性值

   ```py
   class A:
       x = 1
   class B:
       x = 2
   
   class C(A,B):   #继承A的属性和方法
       pass
   num = C()
   num.x
   1
   ```

   * 查询继承顺序mro顺序

   ```py
   B.mro()
   [<class '__main__.B'>, <class '__main__.A'>, <class 'object'>]
   #object为基类，所有的类都会默认继承，默认显示
   ```

   

   * 封装组合：定义一个类，包含其他类

   ```py
   class Dog:
       def say(self):
           print('汪汪汪')
   class Cat:
       def say(self):
           print('喵喵喵')
        
   class Garden:     #定义一个花园类，包括狗类和猫类
       a = Dog()
       b = Cat()
       def say(self): 
           self.a.say()     #注意self前缀
           self.b.say()
   
   w = Garden()
   w.say()
   汪汪汪
   喵喵喵
   ```

   * 多态

     指同一种函数在不同条件下可以有不同的宣发，可以自定定义多态函数



 ## 8.2 方法内的self.x使用

* class类定义的==属性==，在内部使用时，必须加self（--init--内要用，）
* ==形参==使用时候，不加self（def定义方法时，调用形参）
* self特指某个类，self.y表示A类的y属性

```py
class A:
    x = 1
    def hell(self,a,b):              #a,b为hell函数的形参
        self.y = a                   #self.y  self.z中的y，z代表的是hell中具体存在的属性
        self.z = b                   #将形参b的值传给属性z
        self.x = 3                   # 调用x属性，加self
        
a = A()
a.hell(4,5)
a.x
3
a.y                                #若直接定义hell时，y = a，则无法直接调用a.y
4
a.z
5

```

## 8.3 查询属性  __ dict __

```py
class A:
    x = 1
class B(A):
    pass
        
b = B()  #实例对象a
b.x
1          #a中没有x，其父类有，实际显示的a.x是调用父类x的值
b.__dict__
{}         #a中没有属性，x是其父类的属性

#用方法对a插入属性
#构造一个插入函数
class B(A):
    def set_x(self, num):
        self.x = num
        
a.set_x(5)
a.x
5
a.__dict__
{'x': 5}

```

## 8.4 初始化数据 __ init __

* 构造参数必须绑定参数

```py
1#我们可以在定义类时，构造函数（初始化一些数据），当实例对象时，默认初始化这些数据
class Name:
    def __init__(self):
        print('你的名字')
       
zhang = Name()
你的名字       #直接初始化出来

2# 类名后加参数
class Name:
    def __init__(self,yourname):
        self.name = yourname        #构造参数必须必须绑定参数，相当于name为Name的一个属性，由自己定义
        print(f'你的名字是{self.name}')
      
me = Name('张宇乔')   #定义name属性
你的名字是张宇乔
me.name
张宇乔
```

## 8.5 重写方法 super()

* super函数基于mro顺序

* 当想对定义好的类进行修改重写时

* 法1 - 调用未绑定的父类__ init __方法，可以保留父类的属性，并且修改

  ```py
  class A:
      def __init__(self):
          print('我是A')
          
  class B(A):
      def __init__(self)
          A.__init__(self)  #调用未绑定的父类__init__方法,将该方法的结果原封不动的保存下来
          print('我是B')
      
  a = A()
  我是A
  
  b = B()
  我是A
  我是B
  ```

  ```py
  class A:
      def __init__(self,x):
          self.x = x
      def add(self):
          return self.x+2
      
  a = A(3)
  a.add()
  5
  
  #修改
  class C(A):
      def __init__(self,x,y):
          A.__init__(self,x)
          self.y = y                 
      def add(self,z):
          return A.add(self) + self.y + z
  a = C(1,2)
  a.add(2)
  7
  ```

* 调用未绑定的父类__ init __方法，容易造成钻石继承问题

* 钻石继承问题：爷爷，爸爸，姑姑，我四个人买票，爸爸和姑姑同时继承爷爷，爷爷买了两次票

* 法2：super函数

  ```py
  class A:
      def __init__(self,x):
          self.x = x
      def add(self):
          return self.x+2
      
  a = A(3)
  a.add()
  5
  
  #修改
  class C(A):
      def __init__(self,x,y):
          super().__init__()           #A.__init__(self,x),super()函数代替A,括号内不用写变量
          self.y = y                 
      def add(self,z):
          return A.add(self) + self.y + z
  a = C(1,2)
  a.add(2)
  7
  ```

* Python3.x 和 Python2.x 的一个区别是: Python 3 可以使用直接使用 **super().xxx** 代替 **super(Class, self).xxx**

  ```py
  # Python3.x 
  class A:
       def add(self, x):
           y = x+1
           print(y)
  class B(A):
      def add(self, x):
          super().add(x)
  b = B()
  b.add(2)  # 3
  
  # Python2.x  
  class A(object):   # Python2.x 记得继承 object
      def add(self, x):
           y = x+1
           print(y)
  class B(A):
      def add(self, x):
          super(B, self).add(x)
  b = B()
  b.add(2)  # 3
  ```

  

## 8.6 私有变量/方法 __x

* 不希望被外部访问的变量，称为私有变量，用两个_表示

* 通过特定接口访问

* 或者用      _类名__ 私有变量名称  访问

  ```py
  class Name:
      def __init__(self,x):
          self.__x = x          #两条_表示私有变量
      def get_x(self):
          print(self.__x)       #定义访问接口
      def set_x(self,x):        #定义更改接口
          self.__x = x
      def __say(self):
          print("hello")
  
  a = Name(2)        
  a.x
  无法直接访问
  #调用接口
  a.get_x()
  2
  #特殊方法
  a._Name__x
  2
  a._Name__say()
  hello
  ```
  
* _x     下横线开头的变量表示供内部使用的变量，不要轻易修改
* x_     下横线结尾的字母，用于解决自定义名称和 关键字重复的问题

## 8.7 mixin设计模式

* 想要额外添加功能时候，可以单独class一个类，用于功能调用，详见小甲鱼对象Ⅴ

## 8.8 限制__ slots __

* 因为python对象用字典形式保存，以空间换时间(__dict__)

* 为节省空间，当固定好一个对象的属性， 后期不再添加属性时候，可以用slots限制属性数量  (__slots__)

* 用slots限制后，不再有__dict__属性

  ```py
  class A:
      __slots__ = ["x", "y"]   #限制A类中只能有x,y两个属性
      def __init__(self,x):
          self.x = x
          
  a = A(12)
  a.x
  12
  a.y = 14   #可以动态添加属性
  a.z = 22   #报错，不能添加其他属性
  ```

* 继承时候，子类会继承父类的slots，但是子类会有dict

* 即子类可以随意添加属性，保存在dict中

  

# 九 OOP/OPP

## 9.1 区别

![image-20220915194735423](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220915194735423.png)

   关注对象具体的信息，对象怎么具体的做                                                            关注具体的过程，忽略了对象本身



## 9.2 踢掉小孩子

1. opp

   ```py
   cirle = [i for i in range(1, 501)]
   step = 1
   
   while len(cirle) > 1:
       delkid = []
       for kid in cirle:
           if step % 3 == 0:
               delkid.append(kid)
           step += 1
   
       # for kid in delkid:
       #     cirle.remove(kid)
       cirle = [i for i in cirle if i not in delkid]
   
   print(cirle)
   ```

2. oop

   ```py
   class Kid:
       def __init__(self, gid):
           self.right = None
           self.left = None
           self.gid = gid
   
   
   class Circle:
       def __init__(self, count):
           self.head = None
           self.tail = None
           self.count = count
           #初始化一个圆圈
           for i in range(count):
               self.add(Kid(i+1))
   
       def add(self, kid):
           if self.head is None and self.tail is None:
               kid.left = kid
               kid.right = kid
               self.head = kid
               self.tail = kid
           else:
               kid.right = self.tail
               kid.left = self.head
               self.head.right = kid
               self.tail.left = kid
               self.tail = kid
   
       def remove(self, kid):
           if kid is self.head:
               kid.right.left = kid.left
               kid.left.right = kid.right
               self.head = kid.left
           if kid == self.tail:
               kid.right.left = kid.left
               kid.left.right = kid.right
               self.tail = kid.right
           else:
               kid.right.left = kid.left
               kid.left.right = kid.right
               kid.left = None
               kid.right = None
   
   
   circle = Circle(500)
   cur = circle.head  # 头插防断链
   step = 1
   
   while circle.head is not circle.tail:
       cur = cur.left
       if step % 3 == 0:
           circle.remove(cur.right)
       step += 1
   ```




# 十 模块和包

## 10.1 导入模块

![image-20220916175859461](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220916175859461.png)

* 可以简单认为，一个.py文件就是一个模块

* 导入模块

  例如：往p2里导入p1（a,b,add()）

  ![image-20220916180115922](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220916180115922.png)

  ```py
  import ch8.t.t1
  
  print(ch8.t.t1.a)
  ```

  ```py
  #为了简便使用
  import ch8.t.t1 as new
  
  print(new.a)
  ```

  ```py
  #为了更简便实用
  from ch8.t.t1 import a,b,add
  
  print(a)
  
  #不推荐导入t1里的全部内容
  from ch8.t.t1 import *
  ```

## 10.2 对外调用限制__ all __

* 可以设置模块内的对象参数，限定哪些可以被外部导入访问

  ```py
  __all__ = ['a','b','add']
  a = 1
  b = 2
  ```

## 10.3 导入包

* 创建一个包

  ![image-20220916182806677](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220916182806677.png)

![image-20220916182843781](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220916182843781.png)

---------------

* 新建包后，会默认创建__ init __ py文件，在调用包的时候会默认执行

## 10.4 配置阿里云镜像

* C:\Users\zhang\AppData\Roaming目录下创建pip文件

* pip文件下创建pip.ini启动文件

* ```py
  [global]
  timeout = 10000
  index-url = http://mirrors.aliyun.com/pypi/simple/
  trusted-host = mirrors.aliyun.com
  ```



## 10.5 测试语句__ name __

* 在p1.c1中，__ name __ = __ main __，主程序

* p2导入p1, 此时的 __ name __= p1.c1

* p1.c1

  ```py
  def add(x,y):
      return(x+y)
  
  #测试代码
  if __name__ = __main__:
      print(add(1+2))
      
  3
  ```

* p2

  ```py
  from p1.c1 import *
  add(2+3)
  
  5
  #若不用测试代码，则结果返回
  3
  5
  ```

  

# 十一 异常和程序调试

## 11.1 内置异常

![image-20220920151822197](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20220920151822197.png)

## 11.2 捕获异常

```py
try:
    所写的语句
except 错误类型 as 重命名e:
    如果有以上的错误类型，执行该语句
except Exception as e:
    如果有无法识别的错误，转给工作人员处理
    raise ....
else:
    如果没有错误，执行该语句
finally:
    无论是否有错，都要执行该语句
    
```

```py
try:
    a = 1/0
    b = d + 1
    c = 2 + 2
except (NameError, ZeroDivisionError) as e:
    print(type(e))
    print(e)
except Exception as e:
    print(e)
    raise NameError("这是一个什么")
else:
    print("else")
finally:
    print("finally")
```

# 十二 数据库

## 12.1 python内置sqlite3模块

* 轻量级调用本地的数据库

```py
import sqlite3

# 连接数据库
conn = sqlite3.connect('tset.db')
# 拿到游标
cursor = conn.cursor()

# 执行sql
sql = "create table user(id int(11) primary key, name)"
cursor.execute(sql)

# 关闭游标和数据库
cursor.close()
conn.close()
```

# 十三 GUI图形用户接口

## 13.1 基本操作

```py
from tkinter import *

def add(x):
    return x * 1.2

def count_money():
    input_result = inputVar.get()  #得到输入值
    out_result = add(int(input_result))      
    outVar.set(out_result)         #显示输出值


root = Tk()                               # 主窗口
root.title("汇率计算器 v1.0")               # 窗口名
root.geometry("500x300+550+300")          # 窗口长x窗口宽+出现在屏幕的位置

#容器封装,可以让三个框框并列显示
topFrame = Frame(root)
# 定义标签====静态文字
inputLabel = Label(topFrame, text="请输入月收入:", bg="lightblue", font="微软雅黑 13", height=1)  # 在主窗口下设置
inputLabel.pack(side=LEFT, padx=5, pady=5)

# 定义输入框，输入框是用textvarable给inputVar赋值
inputVar = StringVar()
inputEntry = Entry(topFrame, textvariable=inputVar)
inputEntry.pack(side=LEFT, padx=5, pady=5)

# 点击按钮
inputButton = Button(topFrame, text="计算", command=count_money)
inputButton.pack(side=LEFT, pady=5, padx=5)

#封装执行
topFram.pack(padx=5, pady=5)

# 标签显示结果
outVar = StringVar()   # 用.set赋值，标签是用outVar给textvarable赋值
outLabel = Label(root, textvariable=outVar, bg="lightgreen",  font="微软雅黑 13", height=1)
outLabel.pack(pady=5, padx=5)

#执行整个GUI
root.mainloop() 
```

```py
#简写
Label(root, text="").pack(padx=, pady= )
Entryl(root, textvariable="").pack(padx=, pady= )
Button(root, text="", command= ).pack(padx=, pady= )
```



## 13.2 StringVar类型

* StringVar类型是tkinter下的一个类
* 取值

```py
用StringVar.get()取值
```

* 赋值

```py
#重复赋值，会覆盖掉之前的值
用StringVar.set()给其赋值
```

```py
#重复赋值，保留之前值
StringVar.set(StringVar.get()+"  "+ "新的元素")
```

## 13.3 细节操作

* Label

```py
# 静态显示在主窗口下设置标签，内容，标签底色，字体，底色覆盖高度为1的字
#wraplength控制显示字的换行
Label(root, text="请输入月收入:", bg="lightblue", font="微软雅黑 15", height=1, wraplength=800， width=, height= )
.pack(padx=5, pady=5) 
#动态显示
var = Stringvar()
var.set("结果")
Label(root, textvariable=var, bg="lightblue", font="微软雅黑 15", height=1).pack(padx=5, pady=5) 
```

* Entry

```py
#输入值赋值给变量，明文显示
Entryl(root, textvariable="", show="*").pack(padx=, pady= )
```

* Button

```py
Button(root, text="", command= ).pack(padx=, pady= )
#直接关闭root窗口
root.destory()
```

```py
#点击Button弹出提示窗口
from tkinter import messagebox
messagebox.showinfo("错误", "用户名和密码不能为空")
#窗口名字为  错误，内容为  用户名和密码不能为空
```

```py
# 点击时候改变鼠标形状
Button(root, text="", command= , cursor="hand2").pack(padx=, pady= )
```

```py
# 点击时候改变按钮的内容
#法1  用textvariable+set()
jude = False
def do():
    global jude
    jude = not jude
    buttemvar.set("结束") if jude else buttemvar.set("开始”）
    
buttenvar = Stringvar
Butten(root, textvariable=buttenvarm, command=do).pack()
                                                   
# (常用)法2 静态文本text+config()
jude = False
def do():
    global jude
    jude = not jude
    butten_text = "结束" if jude else "开始”  
    doButten.config(text=butten_text)                                                   
                                                   
doButten = Butten(root, text, command=do)
doButten.pack()                                                   
```





## 13.3 登录界面

```py
from tkinter import *
from tkinter import messagebox   #弹出提示窗口


def login():
    username = userVar.get()
    password = passVar.get()
    if username == "" or password == "":
        messagebox.showinfo("错误", "用户名和密码不能为空")
    elif username == 'zhangyuqiao' and password == "000326":
        messagebox.showinfo("信息", "登录成功！")
    else:
        messagebox.showinfo("错误", "用户名或密码错误")


root = Tk()
root.title("登陆界面")
root.geometry("400x200+400+300")

Label(root, text="请输入账号和密码").pack(pady=15)

userFrame = Frame(root)
Label(userFrame, text="用户名:").pack(side=LEFT, padx=5, pady=5)
userVar = StringVar()
Entry(userFrame, textvariable=userVar).pack(side=LEFT,padx=5, pady=5)
userFrame.pack(padx=5, pady=5)

passFrame = Frame(root)
Label(passFrame, text="密   码:").pack(side=LEFT, padx=5, pady=5)
passVar = StringVar()
Entry(passFrame, textvariable=passVar, show="*").pack(side=LEFT,padx=5, pady=5) #密码用明文显示
passFrame.pack(padx=5, pady=5)

buttonFrame = Frame(root)
Button(buttonFrame, text="登录", command=login).pack(side=LEFT, padx=5, pady=5)
Button(buttonFrame, text="取消", command=root.destroy).pack(side=LEFT, padx=5, pady=5)
buttonFrame.pack()

root.mainloop()
```

## 13.4 抽奖小程序

```py
"""
version 1.0
实现简单的抽奖和暂停基本功能
将幸运号码显示出来，并不再重复抽取该好号码
幸运号码标红色
"""
import random

from tkinter import *
# 载入电话号码薄
with open("phone.text", "r") as f:
    numbers = f.readlines()
    numbers = [x.strip("\n") for x in numbers]

# 版本号
version = 2.0
# 判断量
jude = False


def doclink():
    # 点击更改判断值，True执行操作
    global jude, buttenvar
    jude = not jude
    # 更改按钮值
    buttenvar.set("停止") if jude else buttenvar.set("抽奖")
    # 开始/暂停抽奖
    if jude:
        time_counter()
    else:
        # 标签显示幸运号码
        lucky_number = var.get()
        var.set("恭喜{}中奖！".format(lucky_number))
        valueLabel.config(foreground="red")
        # 放入下列中奖列表
        s = "中奖结果：\n" if not result_label_var.get() else result_label_var.get()
        result_label_var.set(s+"  "+lucky_number)
        # 号码列表删除该号码
        numbers.remove(lucky_number)


def time_counter():
    global numbers, jude
    if jude is True:
        var.set(random.choice(numbers))
        root.after(100, time_counter)


root = Tk()
root.title("抽奖小工具v{}".format(version))
root.geometry("900x600+400+200")
# 滚动框
var = StringVar()
var.set("等待抽取")
valueLabel = Label(root, textvariable=var, font="微软雅黑 48 normal")
valueLabel.pack(pady=40, fill=BOTH, expand=True)
# 结果列表框
result_label_var = StringVar()
Label(root, textvariable=result_label_var, font="微软雅黑 15 normal",wraplength=800).pack(side=BOTTOM, pady=30)
# 按钮
buttenvar = StringVar()
buttenvar.set("抽奖")
dobutten = Button(root, textvariable=buttenvar, font="微软雅黑 15 normal", width=20, cursor="hand2", command=doclink)
dobutten.pack(side=BOTTOM, pady=20)

root.mainloop()
```



# 十四 多线程

## 14.1 threading模块

Python通过两个标准库==thread==和==threading==提供对线程的支持。thread提供了低级别的、原始的线程以及一个简单的锁。

threading 模块提供的其他方法：

- threading.currentThread(): 返回当前的线程变量。
- threading.enumerate(): 返回一个包含正在运行的线程的list。正在运行指线程启动后、结束前，不包括启动前和终止后的线程。
- threading.activeCount(): 返回正在运行的线程数量，与len(threading.enumerate())有相同的结果。



Thread类来处理线程，Thread类提供了以下方法:

- **run():** 用以表示线程活动的方法。
- **start()**:启动线程活动。
- **join([time]):** 等待至线程中止。这阻塞调用线程直至线程的join() 方法被调用中止-正常退出或者抛出未处理的异常-或者是可选的超时发生。
- **isAlive():** 返回线程是否活动的。
- **getName():** 返回线程名。
- **setName():** 设置线程名。

## 14.2 创建线程

* 使用Threading模块创建线程，直接从threading.Thread继承，然后重写__init__方法和run方法：
* 将任务函数封装到类里，直接调用start执行任务

```py
import threading

class Mythread(threading.Thread):
    def __init__(self, mythread_name):
        super().__init__(name=mythread_name)     #super().__init__()
                                                 #self.name = mythread_name
    def run(self):
        print("{}正在执行中".format(self.name))

for i in range(10):
    Mythread("test"+str(i)).start()     #实例化类并执行
```

* 直接创建线程
* 实例化线程后，在公布要执行的任务函数

```py
#实例化一个线程，直接实例Thread类，target为要执行的任务函数，用arges内的参数为执行函数的参数赋值，用元组表示，只有一个变量时候，要添加逗号
t = threading.Thread(target=function, arges=())
```

```py
import threading
#任务函数
def show(num):
    print("当前正在执行线程{}".format(num))
#实例化十个线程
for i in range(10):
    t = threading.Thread(target=show, args=(i,))
    t.start()
```

## 14.3 线程合并

* 让主线程等待子线程结束后在结束

```py
import threading
import time


def fun():
    print("子线程开始")
    time.sleep(3)
    print("子线程结束")


print("主线程开始")
t = threading.Thread(target=fun) #部署一个子线程
t.start()
t.join()           #子线程合并到主线程内，子线程执行结束，主线程接着执行
print("主线程结束")

```

## 14.4 守护进程

* 主进程结束的时候，所有子进程（守护进程）全部结束

```py
import threading
import time


def fun():
    print("子线程开始")
    time.sleep(3)
    print("子线程结束")


print("主线程开始")
t = threading.Thread(target=fun) #部署一个子线程
t.setDaemon   #设置wei
t.start()
           #子线程合并到主线程内，子线程执行结束，主线程接着执行
print("主线程结束")
```





















































































































#  函数库

## 内置（BIF）函数

```py
abs(a)       #取绝对值
divmod(a,b)  #求出a/b的 地板数（a//b)和余数（a%b）    %地板数即向下取整，2.3取2，-2.3取-3
int('123')   #字符串化整数，浮点数舍去变整
pow(a,b)     #幂函数，a的b次方，即a**b
input(x)     #输入函数，注意直接获取字符串，若需要数字，需要int（）函数转化
range()      #取数函数，[详见](#循环)
ord('a')     #将字符串转化为对应的ASICC编码
format()     #格式化字符串
map()        #根据提供的函数对指定序列做映射function -- 函数,iterable -- 一个或多个序列
filter
```

 

## 随机数库模块random

```py
#随机生成1~10内随机整数
import random
emp = random.randint(1,10)
```

----------------
```py
#random随机数是靠种子状态设计的，获取种子
x = random.getstate()
random.randint(1,10)
3
random.randint(1,10)
4
random.randint(1,10)
8
#将种子复原，复现相同的随机数
random.setstate(x)
random.randint(1,10)
3
random.randint(1,10)
4
random.randint(1,10)
8
```

## 精确浮点数模块 decmial

```py
import decmial
a = decmial.Decmial('0.3')
#以字符串表示精确的小数
```

## 拷贝模块 copy

```py
imprt copy
A = [[1,2,3],[4,5,6],[7,8,9]]
import copy
B = copy.copy(A)     #多维数组的浅拷贝，拷贝的是路径，受原文件影响，不完全拷贝
C = copy.deepcopy(A) #深拷贝，完全拷贝，不受原文件影响
```

## 关键字模块 keyword

```py
#识别是否为关键字
keyword.iskeyword()
```



