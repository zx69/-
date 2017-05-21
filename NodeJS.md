# NodeJS


*******
from miaov
*******
- 建立在ecmascript上
- 无window,顶层对象为global

## 模块属性
- 每个文件就是一个模块，每个模块都有自己的作用域
- 在某文件中var声明的变量并非全局变量而是该模块作用域下的
- 声明全局变量需显式写 global.a
- __filename：内置属性，指当前文件被解析后的绝对路径
- __dirname: 内置属性，指当前文件所在目录被解析后的绝对路径
- 模块加载机制：
    - require('route');
    - route可绝对可相对
    - 扩展名为js/json/node可不写
    - 如果为同级目录下的模块，不能直接写'xxx.js',而必须写'./xxx.js',xxx.js指node_module里的文件
    - 路径查找顺序：
        1. 按文件名查找
        2. 在文件名后加.js再查找
        3. 在文件名后加.json再查找
        4. 在文件名后加.node再查找
        5. 再找不到返回undefined
- 在模块中定义的变量，作用域为当前模块，外部访问的方法：
    1. 把变量定义为全局变量。——不推荐
    2. 使用模块对象module
        - 保存当前模块有关的信息：
            - id
            - filename
            - loaded
            - parent：module对象,只一
            - children: []
                - [] A中require B,则B为A的children，A为B的parent
            - exports: {} 
                - 为require()的返回值
                - 提供对外接口
- 模块内本身有一个exports对象，一般module.exports === exports
    - 若有一者重新赋值，则相等关系破坏 ——尽量不要这样做

## global对象

### process对象
- global对象
- 返回当前进程信息
- 属性方法：
    - argv: 包含命令行参数的数组[node, .js文件名，命令行参数...]
    - execPath：开启当前进程的绝对路径，即node的安装路径
    - env: 用户环境信息
    - version: node版本
    - versions: node和node依赖包信息
    - pid: 进程pid
    - title: 进程显示名称
    - arch： CPU架构：arm/ia32/x64
    - platform：操作系统平台
    - cwd: 进程工作目录
    - chdir(dir)：改变进程工作目录
    - memoryUsage(): 进程内存使用情况(byte);
    - exit(code): 退出
    - kill(pid): 向进程发送信息
- stdin/stdout: 标准输入输出流
    - console.log内部即通过stdout实现，效果同process.stdout.write('xxx');
    - 默认情况下输入流关闭，开启：process.stdin.resume();
    - process.stdin.on('data',function(chunk){})
        - 按回车触发输入
        - chunk即用户输入数据
        - 

### buffer类
- 用于更好操作二进制数据的类
- 全局类
- new Buffer(num/array): 创建Buffer对象，并分配大小
- new Buffer(string,[encoding]):把Str按encoding(默认UTF-8)转成二进制
    - 参num：size，长度，数据随机填充
    - 参array: size=length,数据即数组元素
    - 参str: size=字节长，英文=str.lengh,中文=str.length*(2~3); 数据即各个字符
    - 分配大小后，buffer的长度固定，不能更改
    - 打印出来可能显示十六进制，方便看
- 方法
    - buf.write(str,[offset],[length],[encoding])
        - offset:从第几位开始写入
        - length:写入的长度

*******
from imooc
*******
- 命令行输入node可进入node环境
- module.exports vs exports
    - 功能相同，调用时有点差别
    - exports为module.exports的方法，module.exports返回实例，而exports返回一个挂载方法
    - 一般推荐用exports方式
- url
    - url.parse('url', 解析方法（T/F, 影响query的方式）,是否忽略协议）
        - 返回一个url对象
    - url.format(url对象) => 'url'
    - url.resolve('path','path') => 'path/path' 拼接url
- querystring 参数处理
    - 序列化：querystring.stringify({...}, 值对连接符（默认为&）,键名值连接符（默认=）) => 'query'
    - 解析：querystring.parse('query',值对连接符（默认为&）,键名值连接符（默认=）) => {}
    - 转义：querystring.escape('str');
    - 反转义：querystring.unescape('str')

- HTTP
    - 状态码
        - 1xx: 请求已接受，继续处理
        - 2xx: 请求成功及处理
        - 3xx: 重定向，需进一步处理才能完成请求
        - 4xx: 客户端错误
        - 5xx: 服务器错误
        - 401：没授权
        - 403：服务器拒绝提供服务
        - 404：没找到
        - 500：
        - 503：服务器当前不能处理请求，可能过段时间恢复

- HTTP服务器
    - createServer(fn)
    - listen(portNum)
    - request(option[, callback])
        - get为request封装
        - 返回clientRequest实例



- 事件模块
    - EventEmitter()
        - var xxx = new EventEmitter(); 
        - 事件发射和监听
        - 支持多事件监听，默认最多10个
        - xxx.setMaxListeners(num) ——设置最多支持事件数
        - xxx.on(name,fn(arg)) --定义事件监听
        - xxx.emit(name,arg) --触发事件
            - 返回 T/F :是否有被监听
        - 移除事件：xxx.removeListener(name, fn)
            - fn为on定义的同一函数
            - 不是写的一样就行，而是要同一引用，因此不能用匿名函数，而应该用定义在外面的具名函数
        - life.removeAllListeners(name) 
            - 移除所有函数
            - 有参时移除所有同名事件
        - xxx.listerners(name) 
            - 获取监听事件本身
            - 有length等属性
        - EventEmitter.listenerCount(xxx, name)
            - 直接获取监听事件数量
