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
