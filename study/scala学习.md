idea快捷键

alt + enter 自动识别错误，导入库

.var  自动声明变量类型

# 0 基础语法

## 0.1 创建

* 常量

```scala
val 名字 ： 常量类型 = xx
val name:String = "zhangyuqiao"
```

* 变量

```scala
var 名字 : 变量类型 = xxx
var name:String = "zhangyuqiao"
```

能用常量，不用变量

声明变量，必须要有初始值

-----

* object对象

```scala
object 对象名字 {
    
}
```

* class类

```scala
// 默认参数为val型，即不可调用访问，不可更改值
class 类名(参数1:类型, 参数2:类型) {
    
}
```

```scala
class student(name:String, var age:Int){}

// 实例化可调用更改
zhang.age = 12
// 无法调用和更改
zhang.name = "tom"
```

* def函数

```scala
def 函数名(参数1:类型, 参数2:类型) : 函数返回类型 ={
    
}
```

```
// unit函数类型，即不返回任何东西，不能赋值给变量
def 函数名(参数1:类型, 参数2:类型) : Unit ={
    
}
```



* 实例化一个类

```scala
val zhang = new 类名(参数1:类型, 参数2:类型))
```

```scala
//1.val型实例,不可能改实例的参数
val zhang = new Student(name="zhangyuqiao",age=23 )

//2.var型实例,可改实例的参数
val zhang = new Student(name="zhangyuqiao",age=23 )
zhang = new Student(name="sam",age=25 )
```

## 0.2 object 和 class

object为伴生对象，通常与同名的class共同使用

需要执行的程序全部在object的main函数下执行

```scala
class Student(name:String, age:Int, money:Int){
  // class定义动态变量以及类的方法
  def printInfo():Unit = {
    println("我的名字是"+name+","+"我的年龄是"+age+Student.school+ money)
  }
}
object Student {
  // object定义静态全局常量
  val school : String = "atguigu"

  //执行实例化时候需要在main函数下
  def main(args: Array[String]): Unit = {
    val zhang = new Student(name = "张宇乔", age = 23, money = 12)
    val liu = new Student(name = "刘四", age = 22, money = 15)

    zhang.printInfo()
    liu.printInfo()
  }

}
```

## 0.3 输出

```
var a:Int = 1
var b:Int = 2
```

```scala
// 1.加号连接
println(a + "你" + b + "好")

// 2.s字符串模板
println(s"这个是${a},这个是${b}")

// 3. f字符串精简小数
var num:Double = 3.14159
println(f"这个是${num}%2.2f")
// 
println(num.formatted("%.3f"))

// 4.printf用%传值
printf("这个是%d，这个是%d", a, b)

// 5.三引号原样输出
var sql:String = s"""  xxxsss  """.stripMargin
```

## 0.4 输入

```scala
    println("请输入大名：")
    val name: String = StdIn.readLine()
    println("请输入年龄：")
    val age : Int = StdIn.readInt()
    println(s"欢迎${name}庆祝${age}岁生日快乐！！！")
```



## 0.5 读写文件

```scala
// 注意：用到库的时候，直接用tab键自动导入

// 读取文件，逐行打印出来
    Source.fromFile("src/main/resources/data.txt").foreach(print)
// 写入文件
// new一个写做机器来new一个文件，写明文件路径，传给writer调用
    val writer = new PrintWriter(new File("src/main/resources/data2.txt"))
    writer.write("hello i am new file!")
    writer.close()
```

# 1 数值类型

![image-20230204193328434](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230204193328434.png)

## 1.1 强制类型转换

```scala
object TestStringTransfer {
 def main(args: Array[String]): Unit = {
 //（1）基本类型转 String 类型（语法：将基本类型的值+"" 即可）
 var str1 : String = true + ""
 var str2 : String = 4.5 + ""
 var str3 : String = 100 +""
 //（2）String 类型转基本数值类型（语法：调用相关 API）
 var s1 : String = "12"
 var n1 : Byte = s1.toByte
 var n2 : Short = s1.toShort
 var n3 : Int = s1.toInt
 var n4 : Long = s1.toLong
 }
}

```

# 2 结构

## 2.1 分支

```scala
if (age < 18){
 println("童年")
 }else if(age>=18 && age<30){
 println("中年")
 }else{
 println("老年")
```

* 三目运算符

```scala
var res = if (age >18) "成年" else "未成年！"
```

## 2.2 for循环

###  2.2.1 for 循环

```scala
// 把1-10转移给i
//前后闭合
for(i <- 1 to 10) {
    println(s"喜欢张宇乔的第${i}天！")
}
```

### 2.2.2 until循环

```scala
// 前闭后开，打印1-9
for(i <- 1 until 10) {
    println(s"喜欢张宇乔的第${i}天！")
}
```

### 2.2.3 循环守卫

```scala
// 相当于break
for(i <- 1 to 3 if i != 2) {
 print(i + " ")
}

//相当于
for (i <- 1 to 3){
    if (i != 2) {
    print(i + " ")
    }
}

```

### 2.2.4 循环步长

```scala
// 1,3,5,7,9
for (i <- 1 to 10 by 2) {
 println("i=" + i)
}
```

### 2.2.5 嵌套循环

```scala
for(i <- 1 to 3; j <- 1 to 3) {
 println(" i =" + i + " j = " + j)
}

// 相当于
for (i <- 1 to 3) {
  for (j <- 1 to 3) {
  println("i =" + i + " j=" + j)
 }
```

### 2.2.6 引入变量

```scala
for (i <- 1 to 3) {
 for (j <- 1 to 3) {
 println("i =" + i + " j=" + j)
 }

//for 推导式有一个不成文的约定：当 for 推导式仅包含单一表达式时使用圆括号，
//当包含多个表达式时，一般每行一个表达式，并用花括号代替圆括号
for {
  i <- 1 to 3
  j = 4 - i
} {
 println("i=" + i + " j=" + j)
}

// 等价于
for (i <- 1 to 3) {
 var j = 4 - i
 println("i=" + i + " j=" + j)
}
```

### 2.2.7 循环返回值

```scala
//  2 3 4 5 6 7 8 9 10
val res = for(i <- 1 to 10) yield i
println(res)

```

## 2.3 while循环

```scala
// 与 for 语句不同，while 语句没有返回值，即整个 while 语句的结果是 Unit 类型()

// 而变量需要声明在 while 循环的外部。
```

```scala
var i = 0
while (i < 10) {
 println("宋宋，喜欢海狗人参丸" + i)
 i += 1
 }
```

```scala
var i = 0
do {
 println("宋宋，喜欢海狗人参丸" + i)
 i += 1
 } while (i < 10)
}

```

## 2.4 程序中断

```scala
// breakable和break都要导入库
object for_for {
  def main(args: Array[String]): Unit = {
    breakable(
      for (i <- 1 to 10) {
        if(i ==3 ){
          break
        }
        println(i)
      }
    )
  }

}
```

# 3 函数

## 3.1 函数和方法区别

```scala
// 函数：定义在main函数里面的函数，可以直接main函数里面调用
object method {
  def main(args: Array[String]): Unit = {
    // 函数
    def Hello (name:String) :Unit = {
      println(s"your name is ${name}")
    }
    // 调用
    Hello("张宇乔")
  }

}
```

```scala
// 方法：定义在class和object里面的函数，统称为方法，在main函数里调用，需要语法
object method {
  def main(args: Array[String]): Unit = {
    // 函数
    def Hello (name:String) :Unit = {
      println(s"your name is ${name}")
    }
    // 调用函数
    Hello.Hello("校长")
    // 调用方法
    method.Hello("小王")
  }
  // 方法
  def Hello(name: String): Unit = {
    println(s"yayay is ${name}")
  }

}

```

## 3.2 可变参数

```scala
// 语法：
def txt(a : int*) = Unit {
    println(a)
}
// 可变参数可以不用传值，也可以传递多个值，打包成一个数组
// 可变参数一半放在最后
```

## 3.3 函数定义简化

```scala
// 1.无返回值，可以不用 Unit和 等号
def f1(name : String) {
    println(name)
}

// 2.函数无参，可以不加小括号,直接调用函数名
def f2 {
    println("hello")
}
f2
```

## 3.4 函数作为值传递

```scala
def main(args: Array[String]): Unit = {
    def f1 (a:Int): Int ={
        println(a+1)
        return a
    }
    // 返回值传递
    val num:Int = f1(2)
    println(num)
    // 函数整体传递
    val f2 = f1 _
```

## 3.5 匿名函数/lambda表达式

```scala
// 如果不关心函数名称，只关心逻辑处理，那么函数名(def)可省略
def f1(name : String):Unit = {
    println(name)
}

// 简化
(name:String) => {println(name)}
```

_______

```scala
// 常用函数:固定好操作，将变动的数据作为传参
// lambda : 固定好传参，将操作（或者说是函数）作为传参
// 注意函数类型：String => Unit   传入Sring类型，无返回
def f1(func:String=>Unit):Unit={
    func("zhang")
}

// 不用lambda
def f2(name:String):Unit={
    println(name)
}
f1(f2)

// 使用lambda
f1( (name:String) => {println(name)} )

// 1. lamba参数类型可省略，根据f1的形参推到，即根据Sting => Unit
f1( (name) => {println(name)} )

// 2. lamba只有一个参数，则小括号可省略，无参数或者多参数不可省略
f1( name => {println(name)} )

// 3. lambda操作只有1个，可省略花括号
f1( name => println(name) )

// 4. 若参数只出现一次，则参数可省略，函数体使用参数可用_代替
f1( println(_))

```

```scala
// 常用：1参数，多操作
// 函数体多个操作间转行分开，不用逗号
f1( name => {println(name)
      println(s"${name}是我名字")
    })

// 1参数1操作
f1(println)
```

## 3.6 高阶用法

### 3.6.1 将一个数组每个元素加1

```scala
object test2 {
  def main(args: Array[String]): Unit = {
    // 数组
    val arr : Array[Int] = Array(1,2,3,4,5)
    // 操作
    def op(ele : Int): Int ={
      ele+1
    }
    // 汇总
    def Add(arr: Array[Int], op: Int => Int): Array[Int] = {
      for (elem <- arr) yield op(elem)
    }
    
    // 结果输出  
    println(Add(arr, op).mkString(","))
  }
}
```

```scala
// 法2
object test2 {
  def main(args: Array[String]): Unit = {
    // 数组
    val arr : Array[Int] = Array(1,2,3,4,5)
    
    // 汇总
    def Add(arr: Array[Int], op: Int => Int): Array[Int] = {
      for (elem <- arr) yield op(elem)
    }

    
    // 结果输出  
    println(Add(arr, _+1).mkString(","))
  }
}

```









