# Golang

## 环境变量

- GOROOT</br>
GO语言的安装目录.
- GOPATH</br>
Go语言的工作目录，分为三个子目录pkg,src,bin
  - src:开发的源代码文件，对应目录称为包。
  - pkg:存放编译后的包文件。
  - bin:存放项目源文件。
- GOBIN</br>
存放项目编译后生成的二进制文件.
- GOPROXY</br>
go get 下载依赖时使用的代理地址.

## 基本语法
- 从helloWorld开始</br>
```
package main

import "fmt"

func main() {
  fmt.Println("Hello World")
}	
```
### 变量与常量
- 变量定义:  
  - var name type (var age int)  
  - var{  
    age int  
    name string  
  }  
  注意，如果没有为变量赋值，那么就会默认为该类型的默认值（int=>0; String=>空）
  - 短变量声明初始化：  
    :=(自动推导) name:="Harry"=>由等号后的数据类型自动为变量定义类型。
- 变量交换
```
package main
import "fmt"
func main(){
    a:=100
    b:=200
    
    a,b=b,a
    fmt.Println(a,b)
}
```
- 匿名变量"-"  
  可以接受任何类型的赋值，但是接收到赋值后会直接丢弃，不做保存。因此可以同时定义多个匿名变量
  ```
  func test()(int,int){
    return 100,200
  }

  func main(){
    a,_ :=test() //只获取第一个返回值

  }
  ```

- 变量的作用域
  - 局部变量  
    函数体内定义的变量，函数的参数和返回值都是局部变量
  - 全局变量
    函数体外定义的变量，可以与局部变量名称一致，但是就近原则使用局部变量。不同源文件中的全局变量可以使用import引用。
- 常量
  - 显式`const URL string = "www.baidu.com"` 定义时声明数据类型
  - 隐式`const URL = "www.google.com"`定义时不声明数据类型
  - 同时定义多个，不同类型常量`const a,b,c = 3.14,"string",false`
- 特殊常量iota
  - 从0开始自增
  ```
  const{
    a = iota //a=0
    b        //b=1
    c        //c=2
    d        //d=3
    e = "Hello"//e="Hello"
    f        //f="Hello"
    g = iota //g=6
    h        //h=7
  }
  //直到下一个const关键字出现时才会被重置为0
  const{
    i=iota   //i=0
    j        //j=1
  }
  ```
### 数据类型
- 数据类型
  - 布尔型(默认值0)
  - 数字型
    - 整型: int(有符号)uint(无符号)byte(uint8)
    - 浮点型 float(默认float64)//输出默认保留6位小数
  - 字符串
    - 单引号=>对应整型int，映射到ASCII表(其他编码表：GBK中文; Unicode全球)
    - 双引号=>String
    - 字符串连接"+"
    - 转义符/
- 类型转化
  转换后的变量：=转换类型(变量)` var a int = 10 b:= float(a)`

- 位运算符
  - 与或非
  - ^异或，不同为1相同为0
  - &^位清空=>a&^b b的每一位如果为0，则取a上对应的位，如果为1，改为0；
  - << n 左移n位，等价于乘2的n次方.右移为除
### 流程控制
- 分支结构
  - if 语句
    - 单分支：if 条件表达式{逻辑语句}(表达式与if间需要空一格)
    - 双分支=>规范格式
    ```
    if 条件表达式{
      逻辑语句
    } else {
      逻辑语句
    }
    //下面这种情况是错误的
    if 条件表达式{
      逻辑语句
    }
    else {
      逻辑语句
    }
    ```
    - 多分支  略。。 
  - switch 语句
    - 
    ```
    var mark int = 83
    switch mark/10{
      case 10:
      fmt.Println("A")
      case 9:
      fmt.Println("B")
      case 8:
      fmt.Println("C")
      case 7:
      fmt.Println("D")
      defult:
      ......
    }
    ```
    switch 后是一个表达式，可以是常量，变量或者有返回值的函数。  
    switch 后可以定义变量，但需要用隐式定义并;结尾
    case 后如果是常量，则1.不能重复 2.数据类型需要与switch 后的数据类型相同。  
    case 后可以接多个，有一个匹配就可以跳转
    fallthrough 关键字进行穿透=>运行至当前语句后，继续执行下一个case中的语句。
- 循环结构  
  for 初始表达式；布尔表达式； 迭代因子{
    循环体
  }
### 函数
- 自定义函数  
  ```
  func 函数名(形参列表)(返回值类型){
    函数体
  }
  ```
  - 命名方法：驼峰命名，   
首字母大写的函数可以被本包及其他包的代码使用(public)  
首字母小写的函数只能被本包代码使用(private)
  - 函数特性：
    - 不支持重载   
    - 支持可变参数`args...int`可以传入多个数量的int类型的数据，传入0，1...n 个
    ```
    func test (args...int){
      //内部处理时将可变参数当作切片处理
      //遍历
      for i:=0;i<len(args);i++{
        fmt.Println(args[i])
      }
    }
    ```
    - 基本数据类型与数组通过值传递，在函数内修改不会影响函数外的值。 如果需要在函数内修改函数外的值，可以使用指针。
    - 函数也是一个数据类型，可以赋值给变量，通过变量可以调用函数。同时函数也可以作为另一个函数的形参
    - 自定义数据类型`type myInt int`(给int数据类型起了一个别名myInt,虽然本质上相同，但编译时认为两者不是同一个数据类型)
    - 可以对返回值进行命名
    ```
    func test(a int b int)(sum int, sub int){
      sub := a-b
      sum := a+b
      return
    }
    ```
- init函数：用于进行初始化操作。  
  优先级：全局变量定义->init函数调用->main函数调用  
  多个源文件中有init函数时，按照导入的顺序执行init函数
- 匿名函数：   
定义时就直接调用，只能使用一次
```
func main(){
  result:= func(num1 int,num2 int){
    return num1+num2
  }(10,20)
  fmt.println(result)
}
```
- 闭包
  - 什么是闭包：  
    一个函数和其相关的环境的组合  
    匿名函数+引用的变量/参数=>闭包
  - 使用场景：
    保留上次引用的值，不需要重复输入就可以使用
- defer关键字
  用于函数执行完毕后及时释放资源   
  在遇到defer关键字后，会将defer后的语句压入栈中，先执行后面的语句。在函数执行完毕后，从栈中取出语句进行执行。！压入栈时的变量的值保持为压入时的状态！
  
### 错误处理
- 错误处理
  - 程序出现错误之后中断，无法继续执行
  - 捕捉机制(defer + recover )
