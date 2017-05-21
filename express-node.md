# express-node

- express-generator
    - 生成目录结构
- .jade 模板引擎
- 默认3000端口
- supervisor: 修改刷新更新，不需重启./bin/www

- 读取静态文件：app.use(express.static(__dirname + '/public'));
- 路由设置单独文件：
```
// routes/index.js
module.exports = function(**app**){
    app.get('/',function(req.res){
    }
    app.get('/admin',function(req,res){
    }
    ...
}
// 引用
var express = require('express');
var app = express();
var routes = require('./routes')(app);
app.listen(3000)
```
> 路由用* 匹配全部路由

## 原理
### 底层：http模块
- Express框架等于在http模块上加了一个中间层
```
// nodejs
app = require('http').createServer(function(req,res){})
// <=====> express
app = require('express')().get('/',function(req,res){})
```

### 中间件（middleware）
- 处理HTTP请求的函数
```
function uselessMiddleware(req, res, next) {
  next();
}
```
- next代表下一个中间件
    - 如带参数，则代表抛出一个错误，参数为错误文本，后面的中间件将不再执行，直到发现一个错误处理函数为止

### use方法
- 注册中间件的方法，返回一fn
- 使用use实现路由：
    - 在内部对访问url(req.url)进行判断
    - app.use('/path',someMiddleware);// 只对**完全匹配**路由实现某中间件

## Express方法
### use别名
    - all/get/post/put/delete
    - get方法的回调函数没有调用next方法，所以只要有一个中间件被调用了，后面的中间件就不会再被调用了
    - 路径允许模式匹配："/hello/:who?"
        - ?表示该参可选 
    - 路径可用正则
### app.set(系统变量，值)
    - 为系统变量指定值
### response对象
- response.redirect方法
    - 指定网址的重定向
- response.sendFile('/path/to/anime.mp4') 
    - 发送文件;
- response.render('index',{message:'Hello World'})
    - 渲染网页, 将message变量传入index模板

### request对象
- request.ip 获得HTTP请求的IP地址
- request.files 获取上传的文件

## 搭建HTTPs服务器
```
var fs = require('fs');
var options = {
  key: fs.readFileSync('E:/ssl/myserver.key'),
  cert: fs.readFileSync('E:/ssl/myserver.crt'),
  passphrase: '1234'
};

var https = require('https');
var express = require('express');
var app = express();

app.get('/', function(req, res){
  res.send('Hello World Expressjs');
});

var server = https.createServer(options, app);
server.listen(8084);
```
## 模板引擎
- 静态页面可用res.sendfile(path)显示，动态页面应用模板引擎

> 使用环境变量设置端口：
var port = process.env.PORT || 3000;
(terminal):PORT=4000 node app.js

