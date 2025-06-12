# go语言测试  
使用`go test`命令。包目录中，所有以`_test.go`为后缀名的源代码文件都是测试的一部分，不会被编译到最终的可执行文件中

- 单元测试函数   
  函数前缀为Test=>测试逻辑行为

- 基准测试函数
  函数前缀为Benchmark=>测试函数性能

- 示例函数
  函数名前缀为Example=>为文档提供示例文档

## 单元测试
每个测试函数都必须导入testing包
```
func TestName(t *testing.T){
    //参数t用于报告测试失败和附加的日志信息
}
```
给出一个单元测试的示例
```
//原函数
func Split(s,sep string)(result []string){
    i := string.Index(s,sep)//查找在s中分隔符第一次出现的索引

    for i>-1{
        result = append(result,s[:i])//将分割前的部分加入result
        s = s[i+1:]//截取分隔符后面的部分作为新的string
        i = strings.Index(s,sep)//继续查找分隔符
    }
    result = append(result,s)
    return
}
// 测试函数
package split
import(
    "reflect"
    "testing"

    func TestSplit(t *testing.T){
        got := Split("a:b:c",":") //程序输出
        want := []string{"a","b","c"} //期望结果
        if !reflect.DeepEqual(want,got){ //slice 不能直接进行比较，借助反射包内的方法比较
            t.Errorf("expected:%v, git:%v",want,got) 
        }
    }
    func TestSplitWithComplexSep(t *testing.T) {
	    got := Split("abcd", "bc")
	    want := []string{"a", "d"}
	    if !reflect.DeepEqual(want, got) {
		    t.Errorf("expected:%v, got:%v", want, got)
	    }
    }   
)
```
在使用go test时，可以用`go test-v` 添加v参数，使其输出完整的测试结果  
可以使用`go test -run`具体到运行哪些函数(指定函数名/相同前缀)  
可以建立子测试用来保证多组数据
```
func TestXXX(t *testing.T){
  t.Run("case1", func(t *testing.T){...})
  t.Run("case2", func(t *testing.T){...})
  t.Run("case3", func(t *testing.T){...})
}
```
- 表格驱动测试 
  通常表格是匿名结构体切片，name属性用来描述特定的测试用例
  ```
    test := []struct{
    name string
    input string
    sep string
        want[]string
    }{
        {"base case", "a:b:c", ":", []string{"a","b","c"}},
        {"wrong sep","a:b:c",",",[]string{"a:b:c"}},
        {"more sep","abcd","bc",[]string{"a","d"}},
        {"leading sep","今年今天今日","今",[]string{"","年","天","日"}}
    } 
    for _,tt := range tests{
        t.Run(tt.name, func(t *testing.T){
            got:= Split(tt.input,tt.sep)
            if !reflect.DeepEqual(got, tt.want){
                t.Errorf("expected:%#v, got:%#v", tt.want, got)
            }
        })
    }  
  ```
go的测试覆盖率 
`go test -cover`可以用于查看测试覆盖率  
`-coverprofile = 文件名`将覆盖率相关的记录信息输出到一个文件，可以使用cover工具处理记录，查看哪些语句块被覆盖了

## 网络测试
