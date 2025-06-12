## json
初版笔记存在不足，重写了一版
<details>
- 什么是json:  
  一种规范的文本数据格式，可以灵活的表示树形结构的数据
  ```
  {
    "node1":{
        "node1-1":1,
        "node1-2":"sample",
        "node1-3":[
            "node1-3-1",
            "node1-3-2",
            "node1-3-3"
        ]
    }
  }
  ```
- json的值类型
  - 四种常规类型：  
    数字  
    字符串  
    布尔值  
    null
  - 两种可内嵌类型：  
    对象/键值对：需要包含在一对{}内,用 "," 隔开,键必须是字符串，值可以是任意类型，且不需要统一。  
    数组：包含在[]内，用","隔开，可以为任意类型且不需要统一
- go的json标准库:
  - 作用:将json转化为struct或将struct转化为json
    ```
 
    //json.Marshal/json.Unnmarshal 序列化与反序列化
    type Person struct{
        Name string
        Age int64
        Weight float64
    }
    func main(){
        //序列化(结构体转为json)
        p1 := Person{
            Name: "奇树"，
            Age: 18,
            Weight 71.5,
        } //struct
        b, err := json.Marshal(p1) // struct->json.string
        if err != nil{
            fmt.Printd("json.Marshal failed, err %v\n",err)
            return
        }
        fmt.Printf("str %s\n",b)

        反序列化(json转为结构体)
        var p2 Person
        err = json.Unmarshal(b, &p2)
        if err != nil{
            fmt.Printf("json.Unmarshal failed,err:%v\n", err)
            return
        }
        fmt.Printf("p2:%v\n",p2)

    }
    ```
- 结构体tag
  用``包裹，可以使用tag来指定json生成的字段名
  ```
  type Person struct{
    Name string `json:"name"` // 指定json序列化时使用小写name
    Age int64
    Weight float64 `json:"-"` //指定json序列化时忽略字段
  }
  //在tag后使用omitempty可以在结构体为空值时自动忽略该字段
  ```

- 结构体嵌套
  ```
  type User struct {
	Name  string   `json:"name"`
	Email string   `json:"email,omitempty"`
	Hobby []string `json:"hobby,omitempty"`
	Profile
    }

    type Profile struct {
	    Website string `json:"site"`
	    Slogan  string `json:"slogan"`
    }

    func nestedStructDemo() {
	    u1 := User{
	    	Name:  "A",
		    Hobby: []string{"B,C"},
	    }
	    b, err := json.Marshal(u1)
	    if err != nil {
    		fmt.Printf("json.Marshal failed, err:%v\n",    err)
	    	return
    	}
    	fmt.Printf("str:%s\n", b)
      //str:{"name":"A","hobby":["B","C"],"site":"","slogan":""} 此时的json为单层的
       //想要改为嵌套的形式，需要定义字段tag并使用嵌套的结构体指针*Profile `json:"profile,omitempty"`
    }
  ```
- 匿名嵌套实现隐私信息保护
  ```
    type User struct {
	    Name     string `json:"name"`
	    Password string `json:"password"`
    }

    type PublicUser struct {
	    *User             // 匿名嵌套
	    Password *struct{} `json:"password,omitempty"`
    }

    func omitPasswordDemo() {
	    u1 := User{
		    Name:     "七米",
		    Password: "123456",
	}
	b, err := json.Marshal(PublicUser{User: &u1})
	if err != nil {
		fmt.Printf("json.Marshal u1 failed, err:%v\n", err)
		return
	}
	fmt.Printf("str:%s\n", b)  // str:{"name":"七米"}
    }

  ```
  由于字段覆盖的优先级，PublicUser重新定义了Password字段，嵌套的同名字段被覆盖，显示定义的标签`json:"password,omitempty"`会完全接管JSON序列化的行为。  
  但是覆盖的只是序列化行为而非数据本身，原始密码仍然存在，可以用PublicUser.User.Password直接访问
- encoder与decoder
  ```
    import "encoding/json"

    //创建decoder

    func main() {
        f,_ := os.Open("test.json")
        defer f.close()

        d:= json.NewDecoder(f)
        var v map[string]interface{}
        d.Decode(&v) //decode 读取下一个json编码并储存在v的指向值中

        fmt.Printf("v: %v\n",v)
    }
  ```
</details>

新版笔记
- 基础用法：Marshal与Unmarshal
  ```
  // 创建数据结构message
  type Message struct{
    Name string
    Body string
    Time int64
  }

  func main(){
    //序列化
    m := Message{"Alice","Hello",12345678}
    b,err := json.Marshal(m)
    if err != nil{
      fmt.Println(err)
      return
    }
    fmt.Printf("b:%q type:%T\n", string(b), b)
    //反序列化
    var m1 Message
    if err := json.Unmarshal(b,&m1); err != nil{
      fmt.Println(err)
      return
    }
    fmt.Printf("m1:%+v\n", m1) //%+v 打印结构体时，不仅输出结构体的值，还会显示每个字段的名称
  }
  ```
- Decoder与Encoder
  ```
  // 函数定义
  func NewDecoder(r io.Reader) *Decoder
  func NewEncoder(w io.Writer) *Encoder

  //函数使用
  func main(){
    dec := json.NewDecoder(os.Stdin)
    enc := json.NewEncoder(os.Stdout)
    for{
      var v map[string]interface{}
      if err:= dec.Decode(&v); err != nil{
        log.Println(err)
        return
      }
      for k := range v{
        if k!= "Name"{
          delete(v,k)
        }
      }
      if err := enc.Encode(&v); err!= nil{
        log.Println(err)
      }
    }
  }
  ```
  该函数返回一个json解码器(*json.Decoder),通过输入可以从`io.Reader`接口中读取数据
  `io.Reader`是标准接口，可传入文件对象，内存缓冲区，字符串读取器，http响应体，字节切片阅读器
- go读取和写入json
  ```
  type car struct{
    ------------
  }

  c:= car{
    -------------
  }
  data, err := json.Marshal(c)
  if err != nil{
    return err
  }
  err = os.WriteFile("文件名"，data,0644)//0644是权限
  if err != nil{
    return err
  }
  //读取
  data, err := os.ReadFile("文件名")
  if err != nil{
    return err
  }
  c := car{}
  err = json.Unmarshal(dat, &c)
  if err!= nil{
    return err
  }
  ```
## http
Go语言内置了net/http包以实现http客户端与服务段

```
//get请求
resp, err := http.Get("http://example.com/")
//post请求
resp, err := http.Post("http://example.com/upload", "image/jpeg", &buf)
//postform请求
resp, err := http.PostForm("http://example.com/form",
	url.Values{"key": {"Value"}, "id": {"123"}})

//在使用完response后必须关闭主体
defer resp.Body.Close()
body, err := ioutil.ReadAll(resp.Body)
```

含参数的get请求:

```
func main(){
    apiUrl := "........../get"
    data := url.values{}
    data.Set("name","A")
    data.Set("age","18") //string
    u, err := url.ParseRequestURI(apiUrl)
    if err != nil {
        fmt.Printf("parse url requestUrl failed,err:%v\n", err)
    }
    u.RawQuery = data.Encode()
    fmt.Println(u.String())
    resp, err := http.Get(u.String())
    if err!= nil {
        fmt.Printf("post failed, err:%v\n", err)
        return
    }
    defer resp.Body.Close()
	b, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		fmt.Printf("get resp failed, err:%v\n", err)
		return
	}
	fmt.Println(string(b))
}
```

## cobra
规范格式

```
·application/
  ·cmd/
    add.go
    your.go
    command.go
    here.go
  main.go

  //建立
  var rootCmd = cobra.Command{}
```

执行顺序：
PersistentPreRun PreRun Run PostRun PersistenPostRun  
Run 与 RunE =>RunE会返回报错  
本地标志，持久标志(子命令均适用)，全局标志(在根命令上定义的标志)

```
package cmd

import (
	"fmt"
	"os"

	"github.com/spf13/cobra"
)

var rootCmd = &cobra.Command{
	Use:"hello",//命令行提示
	Short:"a cobra example",//简短的提示
	Long:"a cobra example",//详细的提示
	PersistentPreRun: func(cmd *cobra.Command, args []string){
		fmt.Println("PresistenPreRun")
	},
	PreRun: func(cmd *cobra.Command, args []string){
		fmt.Println("PreRun")
    },
	Run: func(cmd *cobra.Command, args []string){
		fmt.Println("HelloWorld")
	},
	PostRun: func(cmd *cobra.Command, args []string){
		fmt.Println("PostRun")
	},
	PersistentPostRun: func(cmd *cobra.Command, args []string){
		fmt.Println("PersistentPostRun")
    },
}
var cfgFile string
var userLicense string 

//flag的四种定义方式(持久标志)
func init(){
	rootCmd.PersistentFlags().Bool("viper",true, "Help message")
	rootCmd.PersistentFlags().String("author","Your Name","Help message")
	rootCmd.PersistentFlags().StringVar(&cfgFile,"config","","Help message")
	rootCmd.PersistentFlags().StringVarP(&userLicense,"License","l","","Help message")
	rootCmd.Flags().StringP("source","s","","Help message")
}

func Execute() {

	if err := rootCmd.Execute(); err != nil {
		fmt.Println(err.Error())
		os.Exit(1)
    }
}
```

- 完整的创建流程：
1. 创建一个新目录hello并进入
2. 在当前目录下生成一个cmd文件夹，并在其中创建一个root.go文件，作为命令行应用程序的入口，添加一个名为helloCmd的新命令
3. 初始化go模块
4. 使用`go build -o hello cmd/root.go`生成可执行文件
5. 运行`./hello hello`来执行命令

- 工作原理
  1. 初始化命令: 创建一个根命令并初始化Cobra库，根命令是命令行应用程序的入口，包含多个子命令和相关的参数和标志
  2. 定义命令和子命令: 使用cobra提供的API定义命令和子命令，并定义执行函数
  3. 解析命令行参数: 用户在命令行输入命令时，cobra解析用户输入的命令.参数和标志并调用执行函数处理
  4. 执行命令
  5. 处理可能出现的错误

```
var cmd = &cobra.Command{
    Use:   "say",
    Short: "Print a message",
    Run: func(cmd *cobra.Command, args []string) {
        message, _ := cmd.Flags().GetString("message")
        fmt.Println(message)
    },
}

func init() {
    cmd.Flags().StringP("message", "m", "Hello World", "Message to print")
}
```

上面定义了一个命令行工具say

```
./xxx say //Hello world；使用了默认值，因为没有提供--message/-m参数
./xxx say --message "Good morning" //Good morning; 使用用户指定的消息值
./xxx say -m "Good night" //Good night; 使用短标志
./xxx say "something" -m "other thing" //other thing args参数被忽略，只处理标志值
```

//示例：文件管理器
  功能：列出指定目录下的所有文件和子目录，创建新文件或目录，删除指定的文件或目录

  ```
  //根命令
  var rootCmd =&cobra.Command{
    Use: "filemgr"
    Short: "A simple file manager CLI tool",
    Long: "filemgr is a simple file manager CLI tool "
  }

  //list 子命令
  var listCmd = &cobra.Command{
    Use："list [directory]",
    Short: "List all files and directories in the specified directory"
    Args: cobra.MaximumArgs(1),//命令最多接收一个位置参数不定义时默认为0
    Run: func(cmd *cobra.Command, args []string){
      directory :="."
      if len(args)>0 {
        directory = args[0]
      }
    },

  }
  //create 子命令
  var createCmd = &cobra.Command{
    Use: "create <name>",
    Short: "Create a new file or directory with the specified name"
    Args: cobra.ExactArgs(1), //必须接收1个参数
    Run: func(cmd *cobra.Command, args []string){
      name := args[0]
    },

  }
  //delete 子命令
  var deleteCmd = &cobra.Command{
    Use: "delete <name>",
    Short: "Delete the file or directory with the specified name",
    Args: cobra.ExactArgs(1),
    Run: func(cmd *cobra.Command, args []string){
      name := args[0]
    },
  }
  //初始化函数
  func init(){
    rootCmd.AddCommand(listCmd)
    rootCmd.AddCommand(createCmd)
    rootCmd.AddCommand(deleteCmd)
  } 
  ```

## crypto库与哈希函数
- 签名算法：
  用于校验数字完整性，使用公钥和私钥来实现签名和验证的过程。 私钥用于生成签名，公钥用于验证签名的有效性。
- hash算法的核心特点：
  确定性(相同输入始终产生相同输出)
  不可逆(理论上无法从哈希值还原原始的数据)
  雪崩效应(微小变化会导致输出产生差异)
  固定长度
- HMAC-SHA1的计算过程：选择一个key-对message进行处理-使用SHA-1+密钥计算HMAC值-输出160位哈希值

```
\\基本的HMAC-SHA1的计算
package main

import (
    "crypto/hmac"
    "crypto/sha1"
    "encoding/hex"
    "fmt"
)

func main() {
    key := []byte("my_secret_key")
    message := []byte("hello world")

    // 创建 HMAC-SHA1 哈希器
    mac := hmac.New(sha1.New, key)
    mac.Write(message)
    hmacResult := mac.Sum(nil)

    // 转换为 16 进制字符串
    hmacHex := hex.EncodeToString(hmacResult)
    fmt.Println("HMAC-SHA1:", hmacHex)
}
```

## 第三方日志库logrus
- go语言生成的日志的内容
  - 时间戳:事件发生的具体时间
  - 日志级别：日志的严重程度(trace, debug, info, warning, error, fataland, panic)
  - 来源信息
    - 文件名和行号(记录日志位置)
    - 函数名
    - 进程ID
  - 消息内容：具体的日志信息

- logrus： 
  与标准库logger完全API兼容  
  拥有可拓展的Hook机制，允许使用者通过Hook将日志发到任意地方，或通过Hook定义日志内容和格式    
  可选日志输出格式(JSONFormatter和TextFormatter)

  ```
  //使用示例
  package main
  
  import(
    log "github.com/sirupsen/logrus"
  )

  func main(){
  	//log.WithFields()创建一个带有指定字段的日志条目
  	log.WithFields(log.Fields{
  		"animal":"dog",//创建一个键值对映射
  		}).Info("很多人喜欢狗") //以INFO级别记录日志，包含消息和之前设置的字段
  	}
  	//结构化日志
   	//级别：INFO
  	//内容："很多人喜欢狗"
  	//附加字段：{"animal":"dog"}
  ```

- 日志级别

  ```
  log.Trace("跟踪程序执行的每一步")
  log.Debug("记录关键变量的值与重要分支的选择")
  log.Info("记录程序正常运行时的关键信息")
  log.Warn("记录潜在问题，程序仍能工作但未来可能有问题")
  log.Error("记录错误事件，程序仍能运行但功能可能已受影响")
  log.Fatal("记录致命错误")//记录日志后调用os.Exit(1)，程序终止
  log.Panic("记录不可恢复的错误")//记录日志后调用panic()
  ```

  可以设置日志记录级别，只会记录具有该级别及以上级别任何内容的条目。如：`log.SetLevel(log.InfoLevel)//只会记录info及以上级别`
- logrus结构化 有格式地结构化日志记录可以增强可读性
  
  ```
  log.WithFields(log.Fields{
    "event": event,
    "topic": topic,
    "key": key,
  }).Fatal("Failed to send event")
  ```

- Hook
  - 什么是hook
    监视系统或进程中的各种事件信息，截获发送向目标窗口的信息并处理(例子：截屏取词)
  logrus允许添加日志级别的hook
  
  ```
  import (
    log "github.com/sirupsen/logrus"
    "gopkg.in/gemnasium/logrus-airbrake-hook.v2" // the package is named "airbrake"
    logrus_syslog "github.com/sirupsen/logrus/hooks/syslog"
    "log/syslog"
  )

  func init() {

    log.AddHook(airbrake.NewHook(123, "xyz", "production"))//添加airbrake钩子实例，将error及以上级别日志发送到Airbrake异常追踪

   hook, err := logrus_syslog.NewSyslogHook("udp",  "localhost:514", syslog.LOG_INFO, "")//创建系统日志hook
    if err != nil {
    log.Error("Unable to connect to local syslog daemon")
   } else {
      log.AddHook(hook)
   }//如果连接失败，记录错误日志，如果成功将hook添加到logrus
  }
  ```
  
  - gin框架使用logrus
  //待补充
