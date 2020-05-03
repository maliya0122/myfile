## node.js

## 一、定义与使用

定义：node.js是一个js运行时环境，可以解析和执行js代码。

- 浏览器中的js：EcmaScript，BOM，DOM

- node中的js：EcmaScript，提供一些服务器级别的操作API：文件读写，构建网络服务，http服务器

### 1.创建http服务

```js
var http = require('http');   //加载网络服务模块
var server = http.createServer();    //1.创建server
//2.监听request请求事件，设置请求处理函数
server.on('request',function(req,res){
    var url = req.url
    if(url  === '/'){
        res.end('/')
    }else if(url === '/index'){
        res.end('index')
    }else{
        res.end('hehe')
    }
})
server.listen(3000,function(){   //3.绑定端口号，启动服务
    console.log('服务器启动了')
})
```

**1.端口号：**

ip 地址定位计算机；端口号定位具体的应用程序；

请求我的客户端的地址是： ::ffff:127.0.0.1  13684

这是客户端请求的端口号：13684；

**2.请求头：**显示是中文乱码；

在服务器端默认发送的数据，是utf-8编码格式；但是浏览器不知道你是utf-8编码的内容；

正确的告诉浏览器我给你发送的内容是什么编码的；

```js
res.setHeader('Content-Type', 'text/plain; charset=utf-8')    //普通文本
```

通过网络发送文件，本质上是发送的内容，当浏览器收到服务器响应内容之后，就会根据服务器发的 Content-Type 进行对应的解析处理；

### 2.读取文件

```js
var fs = require('fs');
fs.readFile('./data/a.txt', function (error, data) {
    if (error) {
        console.log('读取文件失败了')
      } else {
        console.log(data.toString())
      }
})
fs.writeFile('./data/你好.md', '大家好，给大家介绍一下，我是Node.js', function (error) {
  if (error) {
    console.log('写入失败')
  } else {
    console.log('写入成功了')
  }
})

```

### 3.exports和module.exports

```js
默认在代码最后有一句：return module.exports,需要导出啥，就挂载在module.exports上;
module.exports.foo =  'xxx';

写的太麻烦了，所以:var exports = module.exports     //两个对象指向了同一个地址
exports.foo =  'xxx';
```

## 二、服务端渲染和客户端渲染

1.客户端渲染：发请求，拿数据，模板引擎渲染；速度快；（异步追加的，查看源代码是看不到的），ajax渲染出来的，爬虫爬不到；不利于搜索引擎搜索；

![image-20200226201641702](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200226201641702.png)

2.服务端渲染：服务器直接渲染解析好了，然后发给客户端；速度慢一点；（能在源码里面搜到的，就是服务端渲染的）；可以被爬虫抓取到；

![image-20200226201727490](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200226201727490.png)

服务端渲染，使用的是art-template；具体用法没用；

### 1.art-template的使用

```js
var template = require('art-template')
var fs = require('fs')

fs.readFile('./tpl.html',function(err,data){
    if(err){
        return console.log('读取文件失败')
    }
	//data：二进制数据，要转换成字符串
    var ret = template.render(data.toString(),{
        name:'小明',
        age:18,
        province:'湖北省',
        hobbies:['吃饭','睡觉','打游戏']
    })

    console.log(ret)   //html字符串
})
```

### 2.访问静态资源

```js
文件路径都想象成url地址；不要写相对路径
主要是判断req.url，如果是/public就可以访问
```

### 3.nodemon工具

```js
npm install --global nodemon
```

## 三、express框架

### 1.基本使用

```js
npm init -y 
npm install -S express
创建app.js文件

var express = require('express')

// 1. 创建 app
var app = express()

//2.省略参数，直接/访问，不需要加/public
app.use(express.static('./public/'))
//url需要加/public进行访问
app.use('/pubilc/',express.static('./public/'))

//3.路由请求地址
app.get('/', function (req, res) {
    var comment = req.query   //请求参数
    res.render('404.html',{
    	title:'标题'
	})
})

app.post('/post', function (req, res) {
  	var comment = req.query   //请求参数
    res.redirect('/')		//重定向
})

//4.监听端口
app.listen(3000, function () {
  console.log('express app is running ...')
})
```

### 2.配置使用art-template

```js
//安装
npm install art-template express-art-template

//配置解析art，html文件
app.engine('html',require('express-art-template'))

//进行渲染，render
res.render('404.html',{
    title:'标题'
})
```

### 3.获取表单数据

```js
1.get方式
var comment = req.query   //请求参数

2.post方式
使用第三方插件：body-parser
安装，配置参照文档，配置之后，req就会多出来一个body属性；

const bodyparser = require('body-parser')
app.use(bodyparser.urlencoded({ extended: false }))
app.use(bodyparser.json())

var comment = req.body
```

## 四、学生信息的增删查改

### 1.初始化

安装express，body-parse，bootsrap等这些，进行配置，静态资源的管理

### 2.获取文件中的数据

```js
1.从文件中读取到的数据是字符串，要转换成对象
var student = JSON.parse(data).student

2.fs.readFile('./db.json','utf8',function(err,data){}
//读取出来的数据是二进制数据，要转化成utf8
方法：第一种是直接写上utf8；第二种：是写成 data.toString()
```

### 3.路由的设计

| 请求方法 | 路径             | 参数                       | 备注         |
| -------- | ---------------- | -------------------------- | ------------ |
| GET      | /students        |                            | 渲染首页     |
| GET      | /students/new    |                            | 渲染添加页面 |
| POST     | /students/new    | name,age,gender,hobbies    | 处理添加页面 |
| GET      | /students/edit   | id                         | 渲染编辑页面 |
| POST     | /students/edit   | id,name,age,gender,hobbies | 处理编辑请求 |
| GET      | /students/delete | id                         | 处理删除请求 |

### 4.路由的使用

```js
express提供了router的使用方式：

导入router.js文件
app.use(router)

router.js文件：
var express = require('express')
//创建路由容器
var router = express.Router()
//创建接口
router.get('/',function(){})
//导出
module.exports  = router
```

### 5.数据的增删改查操作

```js
对文件里的数据进行增删改查操作
exports.find = function(callback){
    fs.readFile(dbPath,'utf8',function(err,data){
        if(err){
            callback(err)
        }
        callback(null,JSON.parse(data).students)
    })
}
callback():里面传入两个参数，err，data
```

