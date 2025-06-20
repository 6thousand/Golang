# gin

## 使用http包搭建网站
```
package main

import (
	"fmt"
	"net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "hello world") //向文件中执行写操作
}

func main(){
	http.HandleFunc("/hello",sayHello) //使用hello时调用sayhello函数
	err := http.ListenAndServe(":8080", nil) //监听端口
	if err != nil {
		fmt.Println("http server start failed, error: ", err)
		return 
	}
}
```

## 使用gin框架搭建
- 特点：使用httprouter，速度快
```
package main

import (
	"github.com/gin-gonic/gin"
)

func  sayHello(c *gin.Context) {
	c.JSON(200, gin.H{  //type H map[string]interface{}
		"message": "Hello Go!",
    })
}
func main(){
	r := gin.Default() //返回默认的路由引擎
	
	r.GET("/hello", sayHello) //定义http方法为get
	//启动服务
	r.Run()
}
```
- RESTful API
  REST：Representational State Transfer  
  每一个URI代表一种资源
  客户端和服务器之间，传递这种资源的某种表现层；  
  客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。
  通过不同的http请求，让同一个url能够实现多个功能  
  例：  
  Get		/book		查询  
  Post		/book		上传  
  Put		/book		更新  
  Delete	/book		删除
  
  ```
  func main() {
	r := gin.Default()
	r.GET("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "GET",
		})
	})

	r.POST("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "POST",
		})
	})

	r.PUT("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "PUT",
		})
	})

	r.DELETE("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "DELETE",
		})
	})
	r.Run()
  }

  ```
  注意：  
  1.url不应该有任何动词。如果动作是四种方法无法达成的，则将其名词化  
  2.url不应该包含版本号，因为不同版本实际上也是同一个资源。版本号可以在http请求的accept字段中说明
## http/template 标准库
- http/template
  html/template 包实现了数据驱动的模板。它可以生成防止代码注入的安全的HTML内容  
  模板文件通常定义为`.tmp`和`.tpl`为后缀，必须使用UTF-8编码  
  模板文件用{{ }}(两个连续的花括号)包裹并标识需要传入的数据  
  传递给模板的数据用.表示，复杂数据用{{.FieldName}}访问  
  除了{{}}包裹的内容以外，其他内容按原样输出 
  ```
  	<!DOCTYPE html>
	<html lang="zh-CN">
	<head>
    	<title>Hello</title>
	</head>
	<body>
	<p>u1</p>
    <p>Hello{{.u1.Name}}</p>
	<p>年龄: {{.u1.Age}}</p>
	<p>性别：{{.u1.Gender}}</p>

	<p>m1</p>
	<p>{{.m1.name}}</p>
	<p>{{.m1.age}}</p>
	<p>{{.m1.gender}}</p>
	</body>
	</html>

	package main

	import (
	"fmt"
	"net/http"
	"html/template"
	)

	func sayHello(w http.ResponseWriter, r *http.Request) {
	//解析模板
	tmpl, err := template.ParseFiles("./hello.tmpl")
	if err != nil {
		fmt.Println("Parse template failed", err)
		return
	}
	//渲染模板
	name := "World"
	err = tmpl.Execute(w, name)//数据传给http服务w
	if err != nil {
		fmt.Println("Execute template failed", err)
		return
	}
	}
	func main(){
	http.HandleFunc("/", sayHello)
	err := http.ListenAndServe(":8080",nil)
	if err != nil{
		fmt.Println("Http server start failed",err)
		return
	}
	}
  ``` 
- 模板语法
  模板语法都包含在{{}}中，其中的.表示当前对象 
  模板中可以声明变量，用来保存传入模板的数据或其他语句生成的结果`$obj :={{.}}` 
- 移除空格
  使用`{{-`去除模板左侧所有的空白符号，使用`-}}`去除模板右侧所有的空白符号
- 条件判断
  ```
  {{if pipeline}} state1 {{end}}
  {{if pipeline}} state1 {{else}} state2 {{end}}
  {{if pipeline}} state1 {{else if pipeline}} state2 {{end}}
  ```
- 遍历
  ```
  {{range pipeline}} T1 {{end}} //如果pipeline的值其长度为0，不会有任何输出  
  {{range pipeline}} T1 {{else}} T0 {{end}}//如果pipeline的值其长度为0，则会执行T0。

  {{range $idx, $hobby := .hobby}}
  <p>i{{$idx}} - {{$hobby}}</p>
  {{end}}  //遍历hobby中所有项
  ```
- with
  创建一块局部的作用域
  ```
  <p>m1</p>
  {{with .m1}}
  <p>{{.name}}</p>
  <p>{{.age}}</p>
  <p>{{.gender}}</p>
  {{end}}
  ```
- 自定义函数
  ```
  func f1(w http.ResponseWriter, r *http.Request){
	//定义一个函数 
	//要么只有一个返回值，要么有两个返回值，且第二个必须是error类型
	visit:=func(name string)(string,error){
		return name + "visited", nil
    }
	//定义

	//解析
	t :=template.New("f.tmpl")
	//告诉模板引擎添加了一个自定义函数
	t.Funcs(template.FuncMap{
		"visit": visit,
	})
	_,err := t.ParseFiles("./f.tmpl") //名字一定要与模板对应
	if err != nil{
		fmt.Printf("parse template failed, err:%v\n",err)
		return
	}
	name :="World"
	t.Execute(w, name)

	//渲染
	}
	func main(){
	http.HandleFunc("/",f1)
	err := http.ListenAndServe(":9000", nil)
	if err != nil{
		fmt.Println("HTTP server start failed", err)
		return
	}
	}
  ```

- template嵌套：
  template中可以嵌套其他template，可以是单独的文件，也可以是通过define定义的
  ```
  <!DOCTYPE html>
  <html lang="zh-CN">
  <head>
  <title> tmpl-test </title>
  </head>
  <body>
  <h1>测试嵌套template语法</h1>
  <hr>
  {{template "ul.tmpl"}} //u1是单独的文件
  <hr>
  {{template "ol.tmpl"}} //o1是通过define定义的
  </body>
  {{ define "ol.tmpl"}}
  <ol>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  </ol>
  {{end}}
  
  func demo1(w http.ResponseWriter, r *http.Request){
	//解析
	t, err :=template.ParseFiles("./tmpltest.tmpl","./ul.tmpl") //嵌套的tmpl写在后面
	if err != nil{
		fmt.Println("parse template failed,err",err)
		return 
	}
	t.Execute(w,nil)
	//渲染
  }
  func main() {
	http.HandleFunc("/tmplDemo",demo1)
	err := http.ListenAndServe(":9000",nil)
	if err != nil{
		fmt.Println("Http server start failed",err)
		return 
	}
	}
  ```
- template继承
  在页面大部分均相同，只有内容部分存在差别就可以使用block减少工作量
  使用block定义根模板与多个区块
  ```
  //定义一个基础模板templates/base.html
	func index (w http.ResponseWriter, r *http.Request) {
	t,err :=template.ParseFiles("./index.tmpl")
	if err != nil{
    	fmt.Println("parse template failed,err",err)
        return 
    }
	msg:= "这是index页面"

	t.Execute(w,msg)
	}
	func home(w http.ResponseWriter, r *http.Request) {
		t,err :=template.ParseFiles("./home.tmpl")
	if err != nil{
    	fmt.Println("parse template failed,err",err)
        return 
    }
	msg:= "这是home页面"

	t.Execute(w,msg)
	}

	func home2(w http.ResponseWriter, r *http.Request) {
	t, err:=template.ParseFiles("./template/base.tmpl","./template/home.tmpl")
	if err != nil{
		fmt.Printf("parse template failed,err:%v\n",err)
		return
	}

	name:="akane"
	t.ExecuteTemplate(w,"home.tmpl",name)
	}


	func index2(w http.ResponseWriter, r *http.Request) {
	t, err:=template.ParseFiles("./template/base.tmpl","./template/index.tmpl")
	if err != nil{
		fmt.Printf("parse template failed,err:%v\n",err)
		return
	}

	name:="akane"
	t.ExecuteTemplate(w,"index.tmpl",name)
	}
	func main() {
	http.HandleFunc("/home", home)
	http.HandleFunc("/index",index)
	http.HandleFunc("/index2",index2)
	http.HandleFunc("/home2",home2)
	err := http.ListenAndServe(":9000", nil)
	if err != nil {
		fmt.Println("HTTP server start failed", err)
		return
	}
	}

	base 文件：
	<!DOCTYPE html>
	<html lang="zh-CN">
	<head>
    <title> 模板继承 </title>
    <style>
        *{
        margin: 0;
        }
        .nav{
            height: 50px;
            width: 100%;
            position: fixed;
            top: 0;
            background-color: burlywood;
        }
        .main{
            margin-top: 50px;
        }
        .menu{
            width: 20%;
            height: 100%;
            position: fixed;
            left: 0;
            background-color: cornflowerblue;
        }
        .center{
            text-align: center;    
        }
    </style>
	</head>

	<body>

	<div class="nav"></div>
	<div class="main">
    <div class="menu"></div>
    <div class="content center">
        {{block "content" .}}{{end}}
    </div>
	</div>

	</body>
	</html>

	//继承模板
	{{/*继承根模板*/}}
	{{template "base.tmpl" .}}
	{{/*重新定义块模板*/}}
	{{define "content"}}
    	<h1>这是index页面</h1>
    	<p>Hello {{.}}</p>
	{{end}}

	{{/*继承根模板*/}}
	{{template "base.tmpl" .}}
	{{/*重新定义块模板*/}}
	{{define "content"}}
   		<h1>这是home页面</h1>
    	<p>Hello {{.}}</p>
	{{end}}
  ```
- 修改默认的标识符
  使用{{}}包裹，但有时会与vue， angularJS等出现冲突，此时需要修改标识符
  `template.New("test").Delims("{[", "]}").ParseFiles("./t.tmpl")`
  ```
  	package main

	import (
	"fmt"
	"html/template"
	"net/http"
	)

	func index(w http.ResponseWriter, r *http.Request) {
		//定义模板
		//解析模板：解释用到了哪些自定义的标识符
		t,err :=template.New("index.tmpl").
			Delims("{[", "]}").
			ParseFiles("./index.tmpl")
		if err != nil{
			fmt.Printf("parse template failed, err:%v\n",err)
			return
		}
		name := "akane"
		err = t.Execute(w,name)
		if err != nil{
			fmt.Printf("execute template failed, err:%v\n",err)
			return
		}
	}

	func main(){
		http.HandleFunc("/index", index)
		err := http.ListenAndServe(":9000", nil)
		if err != nil {
			fmt.Println("HTTP server start failed", err)
			return
		}
	}
  ```

  - text/template 与html/template的区别
  html/template针对的是需要返回html内容的场景，会对有风险的内容进行转义来防止脚本攻击  
  但是如果希望相信用户输入的内容，就可以自行编写一个safe函数并手动返回`template.HTML`类型的内容
  ```
  func xss(w http.ResponseWriter, r *http.Request){
	tmpl,err := template.New("xss.tmpl").Funcs(template.FuncMap{
		"safe": func(s string)template.HTML {
			return template.HTML(s)
		},
	}).ParseFiles("./xss.tmpl")
	if err != nil {
		fmt.Println("create template failed, err:", err)
		return
	}
	jsStr := `<script>alert('嘿嘿嘿')</script>`
	err = tmpl.Execute(w, jsStr)
	if err != nil {
		fmt.Println(err)
	}
  }

  ```
## gin框架
- gin渲染
  - html 渲染
	```
	package main

	import (
		"net/http"
		"github.com/gin-gonic/gin"
	)

	func main(){
		r := gin.Default()
		r.LoadHTMLGlob("templates/**/*")//模板解析,templates下所有的目录中的所有文件

	
		r.GET("/posts/index", func(c *gin.Context){
			//HTTP请求,模板渲染
			c.HTML(http.StatusOK,"posts/index.tmpl", gin.H{ //gin.H 本质上是一个map
				"title": "posts/index",
			}) //或c.HTML(http.StatusOK)
  	  	})
		r.GET("/users/index", func(c *gin.Context){
			//HTTP请求,模板渲染
		c.HTML(200,"users/index.tmpl", gin.H{ //gin.H 本质上是一个map
			"title": "users/index",
		}) //或c.HTML(http.StatusOK)
    })
	r.Run(":8080")

	}
	{{define "posts/index.tmpl"}}
	<!DOCTYPE html>
	<html lang="en">
	<head>
   		<meta charset="UTF-8">
    	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    	<meta http-equiv="X-UA-Compatible" content="ie=edge">
    	<title>posts/index</title>
	</head>
	<body>
    	{{.title}}
	</body>
	</html>
	{{end}}
	```
- 自定义函数
  与标准库中的用法相似

- 静态文件处理：
  html上用到的用于样式处理的css、js文件等.在解析模板之前给出静态文件的地址  
  `r.Static("/static", "./static")`//`/static`是url，`./static`是目录。  
  当发现/static对应的html时，指向对应的/static目录

- json渲染 
  方法1：使用map自行拼接
  ```
  data:=map[string]interface{}{
	"name": "Akane",
	"message": "Hello World!",
	"age": 18,
  }
  ```
  方法2：使用结构体
  ```
  r.GET("/moreJSON",func(c *gin.Context){
	var msg struct{
		Name	string `json:"user"`
		Message	string
		Age		int
	}
	msg.Name = "Akane"
	msg.Message = "Hello World!"
	msg.Age = 18
	c.JSON(http.statusOK, msg)
  })
  r.Run(":8080")
  ```

- 获取querystring参数
  GET请求URL ？后面的是querystring字符串，key=value格式，多个key-value用&链接
  例：/web/search?username=akane&address=hefei
  ```
  func main(){
	//default 会返回一个默认的路由引擎
	r:= gin.Default()
	r.GET("/user/search", func(c *gin.Context){ //获取发送请求携带的query string 参数
		username := c.DefaultQuery("username", "akane")
		address := c.Query("address")
		c.JSON(http.StatusOK, gin.H{
			"message": "ok",
			"username": username,
			"address": address,
		})
	})
	r.Run()
  }
  ```
- 获取form参数
  在登陆、注册时通常使用form表单传递数据
```
package main

import(
	"github.com/gin-gonic/gin"
	"net/http"
)

func main(){
	r:= gin.Default()
	r.LoadHTMLFiles("./login.html")
	//login get
	r.GET("/login", func(c *gin.Context){
		c.HTML(http.StatusOK, "login.html", nil)
	})
	//login post
	r.POST("/login", func(c *gin.Context){
		username := c.PostForm("username")
		password := c.PostForm("password")
		c.HTML(http.StatusOK,"index.html",gin.H{
			"Name": username,
			"Password": password,
		})
	})
	r.Run(":8080")
}


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>login</title>
</head>
<body>
    <form action="">
        <label for="username">用户名</label>
        <input type="text" id="username" name="username"><br>
        <label for="password">密码</label>
        <input type="password" id="password" name="password"><br>
        <input type="submit" value="登录">
    </form>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
</head>
<body>
<h1>Hello,{{.Name}}!</h1>
<p>你的密码是：{{.Password}}</p>
</body>
</html> 
```
- 获取path/uri参数  
  url与URI的区别: 所有URL都是URI，但URI不一定是URL。URI是资源的唯一标识(可能包含位置或名称)，URL是资源的位置(必须包含访问方式)
```
func main() {
	r:= gin.Default()

	r.GET("/user/:name/:age", func(c*gin.Context){
		name := c.Param("name") //接收url中对应位置的参数
		age := c.Param("age") //Param接收到的均为string类型
		c.JSON(http.StatusOK,gin.H{
			"name": name,
			"age": age,
		})
	})

	r.GET("/blog/:year/:month", func(c*gin.Context){
		year := c.Param("year") //接收url中对应位置的参数
		month := c.Param("month") //Param接收到的均为string类型
		c.JSON(http.StatusOK,gin.H{
			"year": year,
			"month": month,
		})
	})
	r.Run(":8080")
}
```

- gin参数绑定  
  ShouldBind()函数会根据输入的content-type自主选择绑定器。  
  如果是GET请求，只使用Form绑定引擎  
  如果是POST请求，首先检查content-type是否为JSON或XML，然后再使用Form(form-data)
```
type UserInfo struct{
	User 		string  `form:"user" json:"user"`
	Password 	string 	`form:"password" json:"psw"`
}
func main() {
	r := gin.Default()
	r.LoadHTMLFiles("./index.html") //加载html文本
	r.GET("/user", func(c *gin.Context){
		var u UserInfo
		err := c.ShouldBind(&u)
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
        }else{
			fmt.Printf("%#v\n",u)
			c.JSON(http.StatusOK, gin.H{
				"status":"ok",
            })
		}

	})

	r.GET("/index", func(c *gin.Context){
		c.HTML(http.StatusOK, "index.html",nil)
	})
	r.POST("/form", func(c *gin.Context){
		var u UserInfo
		err := c.ShouldBind(&u)
		if err != nil{
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		}else{
			fmt.Printf("%#v\n",u)
			c.JSON(http.StatusOK,gin.H{
				"status": "ok",
			})
		}
	})
	r.Run(":8080")

}
```

- 上传文件  
  单个文件上传
```
func main(){
	r:= gin.Default()
	r.LoadHTMLFiles("./index.html") //加载html文本
	r.GET("/index", func(c *gin.Context){
		c.HTML(http.StatusOK,"index.html",nil)
    })
	//上传单个文件
	r.POST("/upload", func(c *gin.Context){
		file, err := c.FormFile("uploadFile")
		if err != nil{
			c.JSON(http.StatusInternalServerError, gin.H{
				"message": err.Error(),
			})
			return 
		}
			log.Println(file.Filename)
			dst := fmt.Sprintf("C:/tmp/%s", file.Filename)
			c.SaveUploadedFile(file, dst)
			c.JSON(http.StatusOK, gin.H{
				"message":fmt.Sprintf("'%s'上传成功", file.Filename),
            })
		})
		r.Run(":8080")
		/* 多个文件上传 
		func main(){
		r:= gin.Default()
		r.POST("/upload", func(c *gin.Context){
			form,_ := c.MultipartForm()
			files :=form.File["uploadFile"]

			for index, file:= range files{
			log.Println(file.Filename)
			dst := fmt.Sprintf("C:/tmp/%s_%d", file.Filename,index)
			c.SaveUploadedFile(file,dst)
			}
			c.JSON(http.StatusOK,gin.H{
			"message": fmt.Sprintf("%d files uploaded!",len(files)),
			})
		})
		router.Run(":8080")

		}*/
}
```

- 请求的重定向  
  请求重定向有两种方式：http重定向/路由重定向
  ```
  //http重定向
  r.GET("/test", func(c *gin.Context){
	c.Redirect(http.StatusMovedPermanently,"http:www.baidu.com/")
  })

  //路由重定向
  r.GET("/test", func(c *gin.Context){
	//指向重定向的URL
	c.Request.URL.Path = "/test2"
	r.HandleContext(c)
  })
  ```

- 路由与路由组  
在gin框架中，路由的机制是将HTTP请求(GET/POST...)映射到特定的处理函数的机制(router.HTTP方法("路径"，处理函数))
```
//普通路由
r.GET("/index", func(c *gin.Context){...})
r.POST("/login", func(c *gin.Context){...})
//匹配所有请求的Any方法
r.Any("/test", func(c *gin.Context){
	switch c.Request.Method{
		case "GET":
		case "POST":
		.....
		default
	}
//为没有配置处理函数的路由添加处理程序
r.NoRoute(func(c *gin.Context){
	c.HTML(http.StatusNotFound,"views/404.html", nil)
})
})

//路由组：拥有共同URL前缀的路由划分为一个路由组，用一对{}包裹同组的路由  
func main() {
	r := gin.Default()
	userGroup := r.Group("/user")
	{
		userGroup.GET("/index", func(c *gin.Context) {...})
		userGroup.GET("/login", func(c *gin.Context) {...})
		userGroup.POST("/login", func(c *gin.Context) {...})

	}
	shopGroup := r.Group("/shop")
	{
		shopGroup.GET("/index", func(c *gin.Context) {...})
		shopGroup.GET("/cart", func(c *gin.Context) {...})
		shopGroup.POST("/checkout", func(c *gin.Context) {...})
	}
	r.Run()
}
//嵌套路由组==>模块化管理路由
api := engine.Group("api")//创建一级路由组'/api'
{
	user := api.Group("user")//创建二级路由组'/api/user'
	{	
		//定义'/api/user/path'的GET请求处理
		user.GET("path", func(context *gin.Context) {
			//GET请求处理逻辑
		})
		//定义'/api/user/path'的POST请求处理
   		user.POST("path", func(context *gin.Context) {
			//POST请求处理逻辑
		})
	}
}
```

- 中间件  
  在开发者处理请求时，可以加入自己的Hook函数，该函数就叫中间件，可以处理一些公共的业务逻辑
  Gin的中间件必须是一个`gin.HandlerFunc`类型，其返回一个 `c*ginContext`
```
//handlerFunc
func indexHandler(c *gin.Context){
	fmt.Println("index")
	c.JSON(http.StatusOK,gin.H{
		"msg": "index",
	})
}

//定义一个中间件m1,统计运行的时间
func m1(c *gin.Context){
	fmt.Println("m1 in")
	//计时
	start := time.Now()
	c.Next()//调用后续的函数
	//c.Abort()//阻止调用后续的处理函数
	cost := time.Since(start)
	fmt.Printf("cost:%v\n", cost.Microseconds()) 
	fmt.Println("m1 out")
}

func m2(c *gin.Context){
	fmt.Println("m2 in")
	c.Next()
	fmt.Println("m2 out")
}
func main(){
	r :=gin.Default()

	r.GET("/index",m1,m2,indexHandler)

	r.Run(":8080")

}
/*输出的结果应该为
m1 in
m2 in
index
m2 out
time
m1 out*/
```


为全局路由注册
```
func main(){
	r :=gin.New()
	//注册全局中间件
	r.Use(StatCost())

	r.GET("/test", func(c *gin.Context){
		name:=c.MustGet("name").(string) //从上下文取值
		log.Println(name)
		c.JSON(http.StatusOK, ginH{
			"message": "Hello world!",
		})
	})
	r.Run()
}
```

为某个路由单独注册,必须符合条件的才能够运行
```
// 
	r.GET("/test2", StatCost(), func(c *gin.Context){
		name:= c.MustGet("name").(string)
		log.Println(name)
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello World",
		})
	})
```
中间件的注意事项  
gin.Default()中默认中间件为Logger和Recovery  
Logger中间件将日志写入gin.DefaultWriter, Recovery中间件会recover任何panic,如果有，会写入500响应码。  
如果不需要使用默认中间件,使用gin.New()新建一个没有任何默认中间件的路由

