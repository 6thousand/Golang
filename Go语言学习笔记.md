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

### 输入输出

### 文件操作
- ```
  file,err := os.Open(./xx.txt=>相对可执行程序路径下的xx.txt文件)(只读)
  if err != nil {
    fmt.Println("错误信息")
    return
  }
  \\读取文件
  for{
    var temp = make([]byte,128)
    n, err :=file.Read(temp)
    if err == io.EOF{
      fmt.Println(string(temp[:n]))
      return
    }
    if err!= nil{
      fmt.Println("%v\n",err)
      return
    }
    fmt.Println("")
  }
  defer file.Close()
  ```     
  - bufio->在io的基础上封装一层API，支持更多的功能   
    !!注意，这里写入的数据会保存到缓冲区，需要调用Flush()函数将缓冲区的数据写入文件   
    使用缓冲区，消耗更多的内存，但是减少了系统调用的次数从而提高性能。       

  ioutil的readflie方法读取整个文件，将文件名作为参数传入
- 写入文件：OpenFile(name string, flag int, perm filemode)(*file, error)  
  ```
  func Write() {
    file,err := os.OpenFile("./1.txt",os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
    if err!= nil{
      //处理报错
    }
    defer file.Close()
    str := "输入的字符串"
    file.Write([]byte(str)) //[]byte
    file.WriteString("输入的字符串") //string

  }

  ``` 

### 反射
- 程序运行时访问，检测，修改自身的能力  
  所有的接口都包含value与type， reflect.TypeOf 与 reflect.ValueOf可以将信息从接口中剥离出来  
  还可以使用方法修改接口中的数据
  ```
  func reflectSetValue(x interface[]){
    v := reflect.ValueOf(x)
    k := v.Elem().kind //type是具体的类型信息:int32,[]string... kind则是更基础的分类: Int,Slice...
    switch k{
      case reflect.Int32:
        v.Elem().setInt(100)//函数为值传递，需要用指针修改值
      case reflect.Float32:
        v.Elem().SetFloat(9.99)
    }
  }
  ```
  使用IsNil()判断指针是否为空，使用IsValid()判断返回值是否有效
- 结构体反射：通过反射得到结构体数据后，可以通过索引获取其字段信息，也可以通过字段名来获取字段信息
  ```
  type student struct {
	Name  string `json:"name"`
	Score int    `json:"score"`
  }

  func main() {
	  stu1 := student{
		Name:  "A",
		Score: 90,
	}

	t := reflect.TypeOf(stu1)
	fmt.Println(t.Name(), t.Kind()) // student struct
	// 通过for循环遍历结构体的所有字段信息
	for i := 0; i < t.NumField(); i++ {
		field := t.Field(i)
		fmt.Printf("name:%s index:%d type:%v json tag:%v\n", field.Name, field.Index, field.Type, field.Tag.Get("json"))
	}

	// 通过字段名获取指定结构体字段信息
	if scoreField, ok := t.FieldByName("Score"); ok {
		fmt.Printf("name:%s index:%d type:%v json tag:%v\n", scoreField.Name, scoreField.Index, scoreField.Type, scoreField.Tag.Get("json"))
    }
  }
  ```
- 反射的缺点：  
  反射中的类型错误在运行时才会引发panic  
  大量使用反射难以理解  
  反射会导致性能低下
### 并发

- 并行 并发 与 串行：  
  并发：同一时间段内执行多个任务  
  并行：同一时刻执行多个任务  
  穿行：依次执行任务
- 进程，线程和协程：  
  进程(process)：程序在操作系统中的一次执行过程，系统进行资源分配和调度的一个独立单位  
  线程(thread)：操作系统基于进程开启的轻量级进程，是操作系统调度执行的最小单位  
  协程(coroutine)：非操作系统提供而是由用户自行创建和控制的用户态‘线程’，比线程更轻量级
  ```
  // 声明全局等待组变量
  var wg sync.WaitGroup

  //创建单个goroutine
  func hello() {
	  fmt.Println("hello")
	  wg.Done() // 告知当前goroutine完成
  }

  func main() {
	  wg.Add(1) // 登记1个goroutine
	  go hello()
	  fmt.Println("你好")
	  wg.Wait() // 阻塞等待登记的goroutine完成
  }
  //创建多个goroutine
  func hello(i int) {
	  defer wg.Done() // goroutine结束就登记-1
	  fmt.Println("hello", i)
  }
  func main() {
	  for i := 0; i < 10; i++ {
		  wg.Add(1) // 启动一个goroutine就登记+1
		  go hello(i)
	  }
	  wg.Wait() // 等待所有登记的goroutine都结束
  }
  ```
- channel：一种实现不同goroutine之间通信交流的通信机制  
  ```
  var 变量名 chan 数据类型
  make(chan 元素类型，[缓冲区大小])
  ch <- 10 // 把10发送到ch中
  x := <- ch // 从ch中接收值并赋值给变量x
  <-ch       // 从ch中接收值，忽略结果
  close(ch)
  ```
- 使用无缓冲的通道时必须要指定接收方，否则会死锁
  ```
  func recv(c chan int) {
	  ret := <-c
	  fmt.Println("接收成功", ret)
  }

  func main() {
	  ch := make(chan int)
	  go recv(ch) // 创建一个 goroutine 从通道接收值
	  ch <- 10
	  fmt.Println("发送成功")
  }
  ```
  ```
  //channel 与 goroutine的组合运用：
  //将1-100的数输入ch1
  func1 f1(ch chan int){
    for i:=0;i<100;i++{
      ch <- i
    }
    close(ch)
  }
  //从h1中读取数字，计算乘方后输入h2
  func f2(ch1 chan int, ch2 chan int){
    for{
      tmp, ok := <- ch1
      if ok{
        break
      }
      ch2<- tmp*tmp
    }
    close(ch2)
  }
  func main(){
    ch1 := make(chan int,100)
    ch2 := make(chan int,100)//初始化内存

    go f1(ch1)
    go f2(ch1,ch2)
    for ret:= range ch2{
      fmt.Println(ret)//遍历ch2并输出
    }
  }

  ```
- 单向通道：可以在函数的参数中声明函数通道的行为是只读/读写....
- SELECT
  ```
  //基本格式
  select {
    case <-ch1:
    case data := <-ch2:
    case ch3 <- 10:
    default:
  }
  //运用：输出1-10的奇数

  func main() {
	  ch := make(chan int, 1)
	  for i := 1; i <= 10; i++ {
		  select {
		  case x := <-ch:
			  fmt.Println(x)
		  case ch <- i:
		  }
	  }
  }
  ```
  第一次循环时i = 1,ch为空，无法读取故输入，第二次i=2，ch满，无法写入故读取.....
- 并发安全和锁  
  同时多个进程修改同一个数据时会存在数据竞争，导致结果与预期不符，可以用锁来解决这一问题。`lock sync.Mutex` 可以保证同一时间有且仅有一个goroutine进入临界区  
  锁包含互斥锁与读写互斥锁，当读多写少的时候，使用读写锁可以显著提升性能。

### socket
应用层与传输层间的抽象软件层，将复杂的TCP/IP协议隐藏，让用户只需要调用socket规定的相关函数就可以进行通信
- go语言实现TCP通信：  
  一个TCP服务端可以连接多个用户端，因此每建立一个链接就可以创建一个goroutine去处理
- 使用go语言的net包创建的TCP服务端代码
  ```
  // tcp/server/main.go

  // TCP server端

  // 处理函数
  func process(conn net.Conn) {
	  defer conn.Close() // 关闭连接
	  for {
		  reader := bufio.NewReader(conn)
		  var buf [128]byte
		  n, err := reader.Read(buf[:]) // 读取数据
		  if err != nil {
			  fmt.Println("read from client failed, err:", err)
			  break
		  }
		  recvStr := string(buf[:n])
		  fmt.Println("收到client端发来的数据：", recvStr)
		  conn.Write([]byte(recvStr)) // 发送数据
	  }
  }

  func main() {
	  listen, err := net.Listen("tcp", "127.0.0.1:20000")//监听
	  if err != nil {
		  fmt.Println("listen failed, err:", err)
		  return
	  }
	  for {
		  conn, err := listen.Accept() // 建立连接
		  if err != nil {
			  fmt.Println("accept failed, err:", err)
			  continue
		  }
		  go process(conn) // 启动一个goroutine处理连接
	  }
  }
  ```
- 使用go语言创建的客户端TCP代码
  ```
  // tcp/client/main.go

  // 客户端
  func main() {
	  conn, err := net.Dial("tcp", "127.0.0.1:20000")
	  if err != nil {
		  fmt.Println("err :", err)
		  return
	  }
	  defer conn.Close() // 关闭连接
	  inputReader := bufio.NewReader(os.Stdin)
	  for {
		  input, _ := inputReader.ReadString('\n') // 读取用户输入
		  inputInfo := strings.Trim(input, "\r\n")
		  if strings.ToUpper(inputInfo) == "Q" { // 如果输入q就退出
			  return
		  }
		  _, err = conn.Write([]byte(inputInfo)) // 发送数据
		  if err != nil {
			  return
		  }
		  buf := [512]byte{}
  		n, err := conn.Read(buf[:])
	  	if err != nil {
	  		fmt.Println("recv failed, err:", err)
	  		return
	  	}
	  	fmt.Println(string(buf[:n]))
	  }
  }
  ```
- 粘包：由于TCP传输特性导致的多段数据连接在一起的情况   
  解决方法：封包：将数据包分为包头与包体。包头长度固定，并且储存了包体的长度
- UDP服务
  ```
  服务端

  func main() {
	  listen,err := net.ListenUDP("udp", &net.UDPAddr{
		  IP:   net.ParseIP("127.0.0.1"),
		  Port: 30000,
	  })
	  if err != nil {
		  fmt.Printf("listen err:%v\n",err)
		  return
	  }
	  defer listen.Close()
	  for{
		  var buf [1024]byte
		  n, addr, err := listen.ReadFromUDP(buf[:])
		  if err!= nil{
			  fmt.Printf("read from udp err:%v\n",err)
		  }
		  fmt.Println("接收到数据：",string(buf[:n]))
		  _,err = listen.WriteToUDP(buf[:n],addr)
		  if err != nil{
		  	fmt.Printf("write to %v err:%v\n",addr,err)
	  	}
	  }
  }

  用户端
  func main() {
	c, err := net.DialUDP("udp", nil, &net.UDPAddr{
		IP: net.IPv4(127,0,0,1),
		Port: 30000,
		Zone: "",
	})
	if err != nil {
		fmt.Printf("dial failed err %v \n",err)
		return
	}
	defer c.Close()
	input := bufio.NewReader(os.Stdin)
	for{
	s,_ := input.ReadString('\n')
	_,err = c.Write([]byte(s))
	if err != nil{
		fmt.Printf("send to server failed, err:%v\n",err)
		return
	}
	//接收数据
	var buf[1024]byte
	n, addr, err := c.ReadFromUDP(buf[:])
	if err != nil {
		fmt.Printf("recv from udp failed,err:%v\n",err)
		return
	}
	fmt.Printf("read from %v, message:%v\n",addr,string(buf[:n]))
	}
  }
  ```