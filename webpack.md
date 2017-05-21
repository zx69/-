# Webpack
- 本质：一个配置文件
    - entry: 入口
    - ouput: 出口
    - module: 模块
- 文件：webpack.config.js
- 工作方式：通过一个给定的主文件（如：index.js），从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个浏览器可识别的JavaScript文件（静态文件）
- 一切皆模块，所有的文件无论是jsx,tsx,html,css,scss,less,png文件，webpack一视同仁为module。并且每个文件[module]都会经过相同的编译工序 loader==> plugin

```
// 需先将相关plugin引入
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports={
    // path里可用node.js的全局变量和方法，如：
    // __dirname:指向当前执行脚本所在目录
    // path.resolve:相对于点一下下cd命令
    // entry也支持数组形式,因为webpack允许多入口点，将加载数组中的所有模块，但以最后一个模块作为输出
    //    entry:['./index']
    entry:{
        // 入口为JS文件
        //配合下面的plugin,可将代码拆分为多个文件
        app:path.resolve(...),
        mobile:path.resolve(...),
        //第三方库可用以下数组拆分为单独文件
        vendors:['jquery',...]
    },
    output:{
        path:...,
        filename: ...,
    },
    // entry和output为必填属性，以下为选填
    (preLoaders:[/*loaders执行前处理，如进行jshint检测 */])
    module:{
        loaders:[
        {
            test:/\.css$/,
            // loaders处理顺序为从右到左
            // 可使用感叹号连接loader,使同一文件能够使用不同类型的loader:
            loader:style!css!sass?sourceMap
            // 数组写法是否可行？['style','css(?sourceMap)',('sass(?sourceMap)')]
            // 使用extract-text-webpack-plugin的写法：ExtractTextPlugin.extract('style', 'css?modules!postcss')
            include:...
        },
        {
            test:/\.(png|jpg)$/
            // 图片小于limit值才转换
            loader:'url?limit=40000'
        },
        /*
        //  另一种图片loader:
        {
            test: /\.(png|jpg)$/,
            loaders: [
              'file-loader?hash=sha512&digest=hex&name=[hash].[ext]',
              'image-webpack-loader?bypassOnDebug&optimizationLevel=7&interlaced=false'
            ]
        }]
        
        */
        {
            test:/\.(js|jsx)?$/
            loader:'babel'
            include:...
            exclude: 'node_module'
            /*
            //query配置一般放到.babelrc中
            query:{
                preset:['es2015']    
            }
            */
        },
        {
            test: /\.json$/,
            loader:'json'
        }
    ],
    }
    
    (postLoaders:[/*loaders执行后处理*/])
    plugin:[
        new Xxxxx({
            ...
        }),
        (new webpack.ProvidePlugin({
            $:'jquery',
            jQuery: 'jquery',
            'window.jQuery': 'jquery'
        })),
        //使用uglify压缩代码
        (new webpack.optimize.UglifyJsPlugin({
            minimize: true
        })),
        (new webpack.optimize.OccurenceOrderPlugin())
        //把入口文件里的数组打包成verdors.js
        (new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.js')),
        (new HtmlwebpackPlugin({
            title:'',
            template: path.resolve('templates','index.html'),
            filename:'index.html',
            // 定义icon图标
            favicon: path.resolve('/src/index.ico'),
            // 该HTML要引用entry里哪些入口，即script-src要引用哪些js文件
            chunks:['app', 'venders'],
            // 把js文件插入body标签中
            inject:'body'
        })),
        (new webpack.HotModuleReplacementPlugin()//热加载插件)
        (new ExtractTextPlugin("[name]-[hash].css"))
    ],
    devServer:{
        contentBase:'./build', //本地服务器所加载的页面所在的目录(编译后目录)
        inline:true, //实时刷新
        port: 9999 (默认8080),
        colors: true,
        historyApiFallback: true, //不跳转
        //设置代理
        proxy:{
            '/api/*':{
                target: 'http://...'
                secure: false
            }
        }
    },
    //用于JS的source-map,css的在loader里加query实现
    devtool:'eval-source-map'...,
    resolve: {
        //查找module的话从这里开始查找
        root: 'E:/github/flux-example/src', //绝对路径
        //自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
        extensions: ['', '.js', '.json', '.scss'],
        //模块别名定义，方便后续直接引用别名，无须多写长长的地址
        alias: {
            AppStore : 'js/stores/AppStores.js',//后续直接 require('AppStore') 即可
            ActionType : 'js/actions/ActionType.js',
            AppAction : 'js/actions/AppAction.js'
        }
    },
    protcss:[
        require('autoprefixer') //调用autoprefixer插件
    ],
    // 用于CDN
    externals: {
        // require("jquery") 是引用自外部模块的
        // 对应全局变量 jQuery
        "jquery": "jQuery"
    }
}
```
## webpack 命令
- webpack --display-error-details 方便出错时能查阅更详尽的信息（比如 webpack 寻找模块的过程），从而更好定位到问题
- webpack --config XXX.js   //使用另一份配置文件（比如webpack.config2.js）来打包
- webpack --watch   //监听变动并自动打包
- webpack -p    //production,生产环境压缩混淆脚本，很大的压缩体积！
- webpack -d    //生成source-map映射文件

## webpack-dev-server
- 启动命令：'webpack-dev-server --hot --inline'

## module-loaders
- 选项：
    - test: 匹配loaders所处理的文件的拓展名的正则表达式
    - loader
    - include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）
    - query：为loaders提供额外的设置选项（可选）
- 常用loaders:
    - css-loader：使能使用类似@import 和 url(...)的方法实现 require()的功能
    - style-loader：将所有的计算后的样式加入页面
        - 通常情况下，css会和js打包到同一个文件中，并不会打包为一个单独的css文件，不过通过合适的配置webpack也可以把css打包为单独的文件的。
    - sass-loader 
        - 编译sass文件
        - 依赖node-sass
    - less-loader
    - stylus-loader 又一种样式预处理器
    - postCSS 类处理css的babel,后处理器
    - url-loader
        - 将图片转为base64编码
    - babel-loader
        - 依赖babel-core
        - babel plugin
            - babel-preset-es2015
            - babel-prest-react
            - babel-preset-react-hmre 
                - 实现React Transform 
                - 包含两模块：
                    - react-transform-hmre : 实现上述热加载
                    - react-transform-catch-errors: 捕获render里的方法，直接展示在界面上
    - jshint-loader
        - jshint检查，用在preloaders
    - imports-loader
    - json-loader:分析JSON文件并把它转换为JavaScript文件
    
## babel配置
- babel完全可以在webpack.config.js中配置，但因为配置项多，为了方便常提取出来放在.babelrc中
- webpack会自动调用.babelrc里的配置
```
{
   "presets": ["react","es2015"] 
}
```

## plugin
- 区别loader:
    - loaders在打包构建过程中用来处理源文件的（JSX，Scss，Less..），一次处理一个，
    - plugin不直接操作单个文件，它直接对整个构建过程起作用
- 分内置和外置两种
- html-webpack-plugin:自动生成HTML5，每次编译都在文件名中插入一个不同的哈希值
- HotModuleReplacement(HMR) 使更新代码以后只更新局部的组件，而非对全局的页面直接强制刷新
    - HMR是一个webpack插件，它让你能浏览器中实时观察模块修改后的效果，但是如果你想让它工作，需要对模块进行额外的配额
    - Babel有一个叫做react-transform-hrm的插件，可以在不对React模块进行额外的配置的前提下让HMR正常工作
        - 实现：
            1. 安装babel-preset-react-hmre
            2. 配置.babelrc:  
            
```
    {
      "presets": ["react", "es2015"],
      "env": {
        "development": {
        "plugins": [["react-transform", {
           "transforms": [{
             "transform": "react-transform-hmr",
    
             "imports": ["react"],
    
             "locals": ["module"]
           }]
         }]]
        }
      }
    }
```
- 优化插件：
    - OccurenceOrderPlugin（内置） :为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID
    - UglifyJsPlugin（内置）：压缩JS代码；
    - ExtractTextPlugin：分离CSS和JS文件，使样式不打包到脚本中，而是独立出来作为.css，然后在页面中以<link>标签引入

## devtool 4选项：
devtool选项     | 配置结果
--------        | ---
source-map | 在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包文件的构建速度；
cheap-module-source-map    | 在一个单独的文件中生成一个不带列映射的map，不带列映射提高项目构建速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；
eval-source-map     | 使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。不过**在开发阶段这是一个非常好的选项，但是在生产阶段一定不要用这个选项**；
cheap-module-eval-source-map | 这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；

## devserver配置选项：
devserver配置选项 | 功能描述
--------          | ---
contentBase | 默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到“public"目录）
port  | 设置默认监听端口，如果省略，默认为”8080“
inline  | 设置为true，当源文件改变时会自动刷新页面
colors  | 设置为true，使终端输出的文件为彩色的
historyApiFallback  | 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html

## 引入样式
- css\style\sass\less...-loader
- loader中配置
- 相关js中import css文件
- 不需要在HTML文件中link-href引入文件

## 加载第三方库
    - jquery等常用库：直接在js文件里import即可
    - 有些老的库不支持AMD/CommonJS，而是依赖于JQ，解法：
        1. 使用webpack.ProvidePlugin
            - 把一个全局变量（$）插入到所有代码中
            - 然后再js文件中直接import该插件
        2. 使用imports-loader
            - 然后在入口文件中import 'imports?jQuery=jquery!./plugin.js'

## 部署上线
- 新增config文件
- 启动命令："build": "NODE_ENV=production webpack --config ./webpack.production.config.js --progress"


## js文件分离打包
- 在entry中分离
- webpack.optimize.CommonsChunkPlugin(打包后的文件名，[要打包的文件]|'')
- 提取多个入口文件的公共脚本部分，然后生成一个公用js 来方便多页面之间进行复用

## 生成多页面
- 在entry中逐个写出
- output.filename： '[name].js'对应entry里的文件生成多个js
- 可自定义HTML模板
    - 自定义HTML
    - 用HtmlwebpackPlugin中配置，有多个模板则要写多个

## 生成Hash来防止缓存
- output.filename:'[name]-[hash].js'




## scripts简写命令
- 在package.json的scripts属性下写简化命令
- package.json对于start命令默认可用npm start启动，而对于其他命令需要用npm run {script name}启动

## CSS module
- webpack支持CSS module: 所有的类名，动画名默认都只作用于当前模块, 不必担心同类名不同组件样式相互污染
- 在css-loader后加？modules

## autoprefixer
- 配合postCSS使用
- 使css自动根据caniuse添加不同前缀

## 使用CDN
- 使某些模块走CDN并以`<script>`的形式挂载到页面上来加载
- 得确保 CDN 文件必须在 webpack 打包文件引入之前先引入
- 使用 externals 属性设置


# 实战
### 初分析：
- 项目会分成三个运行环境：
    - 开发人员在本地跑的开发环境(dev)
    - 测试人员用来做黑盒测试的测试环境(test)
    - 线上运行的生产环境(production)
- 只考虑开发环境(dev)和生产环境(prod)，webpack的配置需要有两套
- 两套配置必然会存在相同的部分，故将公有部分提出来单独做base.js
- http://o86lf0oxm.bkt.clouddn.com/webpack-demo-03.png![](leanote://file/getImage?fileId=58b2eb577afe546b39000009)
- 引入库webpack-merge用于合并base config和特定环境的config
- webpack.config.js只用作类型判断

### config结构
```
//webpack-config/base.js-------
module.exports = {
  //common config
};

// webpack-config/dev.js, webpack-config/prod.js-----
const webpackMerge = require('webpack-merge');
const base = require('./base');
module.exports = webpackMerge(base, {
  //specific config
});

//webpack.config.js-----
const devModule = require('./webpack-config/dev');
const prodModule = require('./webpack-config/prod');
let finalModule = {};
let ENV = process.env.NODE_ENV;     //此处变量可由命令行传入
switch (ENV) {
  case 'dev':
    finalModule = devModule;
    break;
  case 'prod':
    finalModule = prodModule;
    break;
  default:
    break;
}
module.exports = finalModule;
```
### 写package.json
    - 由于*unix和windows设置NODE_ENV的语句有所差异，此处用到了一个库cross-env以达到兼容的目的
```
{
  "name": "webpack-demo",
  "version": "1.0.0",
  "scripts": {
    "start": "npm run dev"
    "dev": "cross-env NODE_ENV=dev webpack-dev-server --inline --hot --host 0.0.0.0",
    "prod": "cross-env NODE_env=prod webpack"
  },
  "devDependencies": {
    "webpack": "^2.2.0",
    "webpack-merge": "^2.6.1"
  }
}
```
### dev环境
- webpack-dev-server

```
module.exports = webpackMerge(base, {
  entry: process.cwd() + '/src/index.js',
  output: {
    filename: '[name].bundle.js'
  },
  devtool: 'eval-source-map'   //enable srouce map
});
```
- htmlWebpackPlugins
- loaders
    - babel
    - css
        -postcss-loader
            - 使用postcss.config.js配置选项
            `
            module.exports = {
              plugins: [
                require('autoprefixer')({browsers: ['last 2 versions', 'iOS 7', 'Firefox > 20']})
              ]
            };
            `
    - html: html-loader??
    - 图片

### prod环境
- 抽出公共部分
    - entry可共用
    - prod的output需要加上文件chunkhash用来刷新缓存,并将文件输出至dist目录
    - HtmlWebpackPlugin公共
    - js、html和图片loader公共，prod的css loader需要使用ExtractTextPlugin将css从js中分离出来
    - 使用UglifyJsPlugin压缩js
- 使用CleanWebpackPlugin清空output目录
    - 构建前先清空，防止出现垃圾文件  
    
```
    // webpack-config/prod.js
    module.exports = webpackMerge(base, {
      ...
      plugins: [
        ...
        const CleanWebpackPlugin = require('clean-webpack-plugin');
        ...
        new CleanWebpackPlugin(['dist'], {
          root: process.cwd(),
          exclude: []
        })
        ...
      ]
      ...
    })
```
### 配置resolve.alias
- 为常用目录配置一个别名alias，模块引入只需用此最为路径起点  

```
    // webpack-config/base.js
    const path = require('path');
    ...
    module.exports = {
      ...
      resolve: {
        extensions: ['.js'],
        alias: {
          src: path.resolve(__dirname, './../src')
        }
      }
      ...
    };
```
- 通常dev环境和production环境的配置参数比如api domain会有差异，所以需要利用alias将用户两个环境配置文件区分开来
- src目录下新建config目录，src/config目录下新增三个文件：base.js、dev.js、prod.js  

```
    // webpack-config/base.js
    export default {
      version: '1.0.0'
    }
    // webpack-config/dev.js
    import base from './base'
    export default {
      ...base,
      env: 'dev'
    }
    // webpack-config/prod.js
    import base from './base'
    export default {
      ...base,
      env: 'prod'
    }
```
- 配置alias  

```
    // webpack-config/dev.js
    const path = require('path');
    ...
    module.exports = webpackMerge(base, {
      ...
      resolve: {
        alias: {
          config: path.resolve(__dirname, './../src/config/dev.js')
        }
      }
      ...
    })
    ...
    // webpack-config/prod.js
    const path = require('path');
    ...
    module.exports = webpackMerge(base, {
      ...
      resolve: {
        alias: {
          config: path.resolve(__dirname, './../src/config/prod.js')
        }
      }
      ...
    })
```




