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
- 系统函数：
  - 字符串函数  
    - 字符串长度len(str)
    - 遍历字符串
      - for-range语句
    ```
    for i, value := range str{}
    fmt.Printf("索引为：%d, 值为%c \n"，i, value)
    ```
      - 切片 `r := []rune String`
    ```
    for i:=0;i<len(str);i++{
      fmt.Println(r[i])
    }
    ```  
    - 字符串转整数 `strconv.Atoi` 整数转字符串 `strconv.Itoa`
    - 统计字符串中有多少指定的字符 `strings.Count("原字符串","目标字符串")`
    - 不区分大小写的字符串比较 `strings.EqualFold`
    - 返回在字符串中第一次出现某字符的索引 `strings.Index("字符串"，"字符")` 若无返回-1
    - 字符串替换 `strings.Replace("原字符串","目标字符串","替换的字符串"n(替换的个数，-1表示全部替换))`
    - 字符串切割`strings.Split("原字符串"，"切割标识")`->输出字符串数组
    - 大小写切换`ToUpper, ToLower`
    - 将字符串左右空格切除`strings.TrimSpace`
    - 将字符串左右指定字符切除`strings.Trim，strings.TrimLeft，strings.TrimRight`
    - 检查字符串是否以指定的字符串开头/结尾`strings.HasPrefix，strings.HasSuffix`
  - 日期函数
    - `time.now()` 返回time.Time类型的函数
    - `now.format(2006/01/02 15/04/05)`
  - 内置函数(不需要导包直接使用)  
    `new(type) *type`创建内存，实参为数据类型，返回值是一个指针。
### 错误处理
- 错误处理
  - 程序出现错误之后中断，无法继续执行
  - 捕捉机制(defer + recover )
  ```
  func test(){
    defer func(){
      err:=recover()
      if err!=nil{
        fmt.Println("捕获到错误")
        fmt.Println("错误是"，err)
      }
    }()
    num1 := 10
    num2 := 0
    result:= num1/num2
    fmt.Println(result)
  }
  ```
- 自定义错误: errors.New("错误信息")
  ```
  func main(){
    err :=test()
    if err!= nil{
      fmt.Printf("自定义错误"，err)
    }
    fmt.Println("程序运行结束")
  }
  func new(){
    num1:=10
    num2:=0
    if num2==0{
      return errors.New("除数不能为0")
    } else {
      result = num1/num2
      fmt.Println(result)
      return nil //无报错时返回0值
    }
  }
  ```
### 数组
  - 数组遍历
    - for range
    ```
    for key(索引) value(索引值) := range coll//key与value都是局部变量
    {
      fmt.Printf("第 %d 个的值为 %d",key+1,value)
    }
    ```
  - 数组初始化 
    ``` 
    var arr1 [3]int = [3]int(3,6,9)
    var arr2 = [3]int（1，4，7）
    var arr3 = [...]int(4,5,6,7)
    var arr4 = [...]int{2:66,0:33,1:99}//索引：索引值
    ```
  - 数组特点:  
  1.数组的长度也是类型的一部分  
  2.数组通过值传递，在函数外修改数组值需要用指针
  - 二维数组及初始化
  var arr[2][3]int = [2][3]int{{1,4,7},{2,5,8}}
  - 二维数组遍历
  ```
  for key,value :=range arr{
    for k,v :=range value{
      fmt.Printf("arr[%v][%v] = %v"key,k,v)
    }
  }
  ```
### 切片
- 定义：切片是一个包含三个字段的数据结构：  
  1.指向底层数组的指针  
  2.切片的长度  
  3.切片的容量
  切片元素个数len, 容量cap
- 创建切片
  - `var slice []int = intarr[1:3]//从intarr中索引[1..3)位置切出的片段(左闭右开)` 
  - `slice:=make([]int,4,20)`数组对外不可见，不能直接访问。必须通过切片才能间接访问元素
- 切片的注意事项
  - 1.切片定义后需要给定具体数组才能使用
  - 2.切片操作不能越界
  - 3.可以对切片再次切片，更改切片中的值同时会更改对应数组里的值
- 切片扩容的原理  
  将老数组扩容为新数组  
  创建一个新数组，将老数组中的元素复制过去，并在新数组后追加新的元素。  
  切片也可以append切片
  `slice = append(slice,slice1...)`
- 切片拷贝`copy(b,a)`将a中内容拷贝到b

### 映射
- 基本语法 var map变量名 map[keyType]valueType
  ```
  var a map[int]string
  a = make(map[int]string,10)//创建的映射a能够储存10个键值对
  ```
- key 部分不能使用slice, function, map....
- map 的 key-value是无序的; key不可以重复，如果重复了则新的value会替换掉前一个value
- 映射的操作
  - 1.增添/修改
  - 2.删除 delete(map,key) key存在就删除键值对，不存在也不报错
  - 3.清空操作：遍历删除/重新新建一个map，自动舍弃掉现有的
  - 4.查找： value,flag = map[key]
  - 5.遍历：只能使用for range, 形式与数组相似
- 映射嵌套
  ```
  a :=make(map[string]map[int]string)
  a["班级1"] = make(map[int]string,2)
  a["班级1"][20096677] = "小明"
  a["班级1"][20096678] = "小飞"

  a["班级2"] = make(map[int]string,2)
  a["班级2"][20096679] = "小亮"
  a["班级2"][20096680] = "小光"
  for k1,v1:= range a{
    fmt.Println(k1)
    for k2,v2 := range v1{
      fmt.Printf("学号: %v, 姓名: %v",k2,v2)
    }
    fmt.println()
  }
  ```
### 泛型
- 类型定义中带有类型参数的叫做泛型类型=>定义的类型包含大于一种数据类型
  `type mySlice[P int | string] []P//类型参数P可以为int 类型或string类型`
- 声明泛型类型的变量时需要传递真实的数据类型。实例化得到了实际类型的变量
- ~的使用
  ```
  type intSlice[P~int] []P
  _ = intSlice[int]{}
  type myInt int
  type yourInt myInt
  _ = intSlice[myInt]{}
  _ = intSlice[yourInt]{}
  ```
- 基于泛型类型，继续定义泛型类型
  ```
  typr mySlice [T int | string | float32 | float64] []T
  type myStruct[T float32 |float64 |bool] struct{
    FieldA mySlice[T]
  }
  ```
  想要定义可以是多个类型的，可以使用interface方式实现
### 接口
- 定义
  接口定义了抽象的类型，只要实现了该方法的类型都可以称为该类型  
  ```
  type 接口类型名 interface{
    方法名1（参数列表1） 返回值列表1
    方法名2（参数列表2） 返回值列表2
  }
  ```
- 使用值接收者实现接口与使用指针接收者实现接口的区别
 ```
 type mover interface(){
  move()
 }
 type person struct{
  name string
  age int
 }
func (p person) move() {
  fmt.Printf("%v在跑"，p.name) //  使用值接收者
 }

func (p *person) move() {
  fmt.Printf("%v在跑"，p.name) //  使用指针接收者
 }

func 
 func main() {
  var m mover
  p1 := person{  //使用值接收者
    name = "小明"，
    age = 10，
  }
    p2 := &person{  //使用值接收者
    name = "小红"，
    age = 18，
  }
  m = p1
  m.move()
  fmt.Println(m)
 }
 ```
  - 一个类型可以实现多个接口，接口之间可以嵌套
  - 空接口是没有定义任何方法的接口，故任何类型都实现了空接口  
    因此空接口可以用来储存任何类型的变量(可以结合映射使用)
