# ES6

[TOC]

# let & coust
## let命令
- let命令所声明的变量，只在let命令所在的代码块内有效。

- let不像var那样会发生“变量提升”现象。==> 变量一定要在声明后使用，否则报错。

```
var tmp = 123;
if (true) {
// TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```
- let不允许在相同作用域内，重复声明同一个变量。

### 块级作用域
- let实际上为JavaScript新增了块级作用域
- ES5只有全局作用域和函数作用域，没有块级作用域，循环、条件不属块，声明不会封闭。

- ES6允许块级作用域的任意嵌套。
外层作用域无法读取内层作用域的变量。
内层作用域可以定义和读取外层作用域的同名变量。

- 块级作用域的出现，实际上使得获得广泛应用的立即执行匿名函数（IIFE）不再必要了。
```
// IIFE写法
(function () {
  var tmp = ...;
  ...}());
// 块级作用域写法
{
  let tmp = ...;
  ...}
```
- ES5规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。以下声明非法：
```
// 情况一
if (true) {
  function f() {}}
// 情况二
try {
  function f() {}} catch(e) {}
```
- ES6引入了块级作用域，明确允许在块级作用域之中声明函数。 并且规定，块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。——此规定对老代码影响很大，故ES6规定浏览器的实现可以不遵守。  
考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。
```
// 函数声明语句
{
  let a = 'secret';
  function f() {
    return a;
  }}
// 函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };}
``` 


## const命令
- const声明一个只读的常量。一旦声明，常量的值就不能改变。
- const一旦声明变量，就必须立即赋值，不能留到以后。
- 所谓不能改变，主要表现在对基本类型不能重新赋值；
- 对于引用类型，变量名不指向数据，而是指向数据所在的地址。const命令只是保证变量名指向的地址不变，并不保证该地址的数据不变，依然可以为其添加新属性，所以将一个对象声明为常量必须非常小心。
```
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```
- 常量用全大写字母表示;

#### 想将对象冻结，应该使用Object.freeze方法

```
const foo = Object.freeze({});
// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```
上面代码中，常量foo指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。
- 彻底冻结
```
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, value) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```
#### 声明变量方法：
- ES5：var、function
- ES6：var、function、let、const、import、class

## 全局对象的属性
未声明的全局变量，自动成为全局对象window的属性，这被认为是JavaScript语言最大的设计败笔之一。  
从ES6开始，全局变量将逐步与全局对象的属性脱钩。  
**let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。**


# 变量解构赋值
## 数组 
```
let [a, b, c] = [1, 2, 3];
```
- 数组的元素是按次序排列的，变量的取值由它的位置决定
- 要求：模式匹配
- 只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值
- 可用于嵌套数组
- 默认值
```
let [x, y = 'b'] = ['a']; // x='a', y='b'
```
- ES6 内部使用严格相等运算符（===），判断一个位置是否有值。=> 数组成员必须严格等于undefined，默认值才会生效的。
```
let [x = 1] = [null];
x // null
```
## 对象
```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
```
- 对象的属性没有次序，变量的取值由同名属性决定。
- 实质：对象取默认值实为以下简写：
```
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```
真正被赋值的是对象的值，而不是对象的属性。属性只用于匹配模式
```
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```
- 使用解构对象重新赋值而非声明变量时，需加（）：
```
let foo;
({foo} = {foo: 1}); // 成功
```
解析器会将起首的大括号，理解成一个代码块，而不是赋值语句。

- 可用于嵌套对象
- 默认值
```
var {x, y = 5} = {x: 1};
x // 1
y // 5

var {x:y = 3} = {};
y // 3
```
- 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。
```
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```
## 字符串
- 转类数组，有length属性
```
const [a, b, c, d, e] = 'hello';
let {length : len} = 'hello';
```

## 数值 & 布尔值
- 解构赋值的规则是，只要等号右边的值不是对象，就先将其转为（基本包装）对象。
```
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```

## 函数参数
- 默认值
```
function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
<---------------------------->
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```
以上例2为函数move的参数指定默认值，而不是为变量x和y指定默认值，所以会得到与前一种写法不同的结果。


### 圆括号问题
- 对于编译器来说，一个式子到底是模式，还是表达式，没有办法从一开始就知道，必须解析到（或解析不到）等号才能知道。
- ES6规定，只要有可能导致解构的歧义，就不得使用圆括号。
- 实际使用中，可以使用圆括号的情况只有一种：赋值语句的非模式部分；变量声明（函数参数也属变量声明）及模式部分不可加圆括号。
- 建议只要有可能，就不要在模式中放置圆括号。

## 用途示例
1. 交换变量的值
```
let x = 1;
let y = 2;

[x, y] = [y, x];
```

2. 从函数返回多个值  
函数只能返回一个值，如果要返回多个值，可将它们放在数组或对象里返回，再用解构赋值取出。

3. 函数多参数的定义和调用
4. 提取JSON数据
```
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
5. 函数参数的默认值  
=> 避免了在函数体内部再写var foo = config.foo || 'default foo'这样的语句

6. 遍历Map结构
7. 输入模块的指定方法
```
const { SourceMapConsumer, SourceNode } = require("source-map");
```

# 字符串的扩展
### 编码问题
- 字符的Unicode表示法
- codePointAt()
JavaScript内部，字符以UTF-16的格式储存，每个字符固定为2个字节。对于那些需要4个字节储存的字符（Unicode码点大于0xFFFF的字符），JavaScript会认为它们是两个字符。
- String.fromCodePoint()
- 字符串添加了遍历器接口，可以被for...of循环遍历。
- at()
- normalize()

### 包含关系
- ES5：只有indexOf方法
- str.includes(partOfStr,开始位置) => T/F  
表示是否找到了参数字符串。
- str.startsWith(partOfStr,开始位置) => T/F  
表示参数字符串是否在源字符串的头部。
- str.endsWith(partOfStr,结束位置) => T/F  
表示参数字符串是否在源字符串的尾部。

### 重复
- str.repeat(n)

### 字符串补全长度
str.padStart/padEnd(length, addStr);
- 省略参2时默认用空格补全
- 用途
    - padStart数值补全指定位数
    - 提示字符串格式
    ```
    '09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
    ```
### 模板字符串
- 识别换行、缩进
- \` 转义`
- 之中还能调用函数
```
function fn() {
  return "Hello World";
}

`foo ${fn()} bar`
// foo Hello World bar
```
- 可嵌套
- 引用模板字符串本身
```
// 写法一
let str = 'return ' + '`Hello ${name}!`';
let func = new Function('name', str);
func('Jack') // "Hello Jack!"

// 写法二
let str = '(name) => `Hello ${name}!`';
let func = eval.call(null, str);
func('Jack') // "Hello Jack!"   
```
### 标签模板：函数名\`模板字符串\`
- 使用模板解析后的参数调用函数
- 模板解析方式：
    - 参1：[模板字符串中那些没有变量替换的部分];
    - 参n: 模板中的变量
    - 首位为变量时返回空串''
```
var a = 5;
var b = 10;

tag`Hello ${ a + b } world ${ a * b }`;
// 等同于
tag(['Hello ', ' world ', ''], 15, 50);
```
- 应用：
1. HTML转义
2. 多语言转换
3. 模板字符串本身没有条件判断和循环处理功能，但是通过标签函数，你可以自己添加这些功能。

- String.raw()
    - 完全转义：返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串
    - 常用于模板字符串的处理函数



# 正则的扩展
### 构造函数替换flag
```
new RegExp(/abc/ig, 'i').flags
// "i"
```
> 字符串对象可使用正则的4个方法:
> - str.match()  
> - str.replace()  
> - str.search()  
> - str.split()

### u操作符
### y操作符
- 隐含^的全局匹配符
- str.sticky => T/F 是否设置了y

### /xxx/.flage
- => reg修饰符

> .字符无法代表以下字符：\n  \r  行分隔符 段分隔符

# 数值的扩展
- 二/八进制表示法
- Number.isFinite(num) 
- Number.isNaN(num)  
<--->区别ES5:    
传统方法先调用Number()将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，非数值一律返回false。

- Number.parseInt/parseFloat()  
<--->区别ES5:    
所属对象从window移植到Number上

- Number.isInteger()
    - 判断一个值是否为整数
    - JavaScript内部，整数和浮点数是同样的储存方法，所以3和3.0被视为同一个值。

- 极小的常量Number.EPSILON
    - = 2.220446049250313e-16
    - 为浮点数计算，设置一个误差范围.浮点计算误差能够小于Number.EPSILON，我们就可以认为得到了正确结果

- 安全整数和Number.isSafeInteger()
- 指数运算符 **

### Math扩展
- Math.trunc(num) 去除一个数的小数部分，返回整数部分
- Math.sign(num)
    - 参数为正数，返回+1；
    - 参数为负数，返回-1；
    - 参数为0，返回0；
    - 参数为-0，返回-0;
    - 其他值，返回NaN
- Math.cbrt(num) 计算一个数的立方根
- Math.clz32(num) 返回一个数的32位无符号整数形式有多少个前导0。
    > JavaScript的整数使用32位二进制形式表示
- Math.imul(n,m) 返回两个数以32位带符号整数形式相乘的结果，返回的也是一个32位的带符号整数。
- Math.fround(num)
- Math.hypot(n,m,...) 返回所有参数的平方和的平方根  
...

# 数组的扩展
### Array.from(obj,fn，context)
- 用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括ES6新增的数据结构Set和Map）
- 所谓类似数组的对象，本质特征只有一点，即必须有length属性。因此，任何有length属性的对象，都可以通过Array.from方法转为数组。
```
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
```
- 常见的类似数组的对象是DOM操作返回的NodeList集合，以及函数内部的arguments对象
- 将类数组对象转为数组后就可使用数组的方法，如forEach
- 参2：类[].map=>[],处理数组项
- 参3：传入绑定this，用于参2使用

- 特殊应用
    1. 用参1指定参2运行次数
    ```
    Array.from({ length: 2 }, () => 'jack')
    // ['jack', 'jack']
    ```
    2. 转string为数组
    - 优于str.split(''):避免JS将大于\uFFFF的Unicode字符，算作两个字符的bug。  
    
<----->
### 扩展运算符（...）
- 可将可遍历（iterable）对象转为数组
- 调用的是遍历器接口（Symbol.iterator），如果一个对象没有部署这个接口，就无法转换
- 不支持类数组转换

> ES5替代方法：Array.prototype.slice.call(xxx) 或 [].slice.call(obj)


### Array.of(arguments) => [arguments]
- 弥补Array(arguments)构造函数不足：转换不统一，当参仅一是取参为length

### copyWithin(target,start,end) 数组元素移动替换
...

### arr.find(fn(value,index,arr)，context)  
=> 返回第一个为true的成员
=> 全不符返回undefined
context:传入绑定this，用于fn使用
### arr.findIndex(fn(value,index,arr)，context)
=> 返回第一个为true的成员的位置
=> 全不符返回-1

### arr.fill(target,start,end)  使用给定值，填充一个数组

### arr.entries() / keys() / values()
- 遍历数组，=>遍历器对象, 可用for...of遍历
- keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历。

### arr.include() => T/F  
- 某个数组是否包含给定的值，与字符串的includes方法类似
- <--->arr.indexOf()
1. 找到参数值的第一个出现位置，比较是否不等于-1
2. 内部使用严格相当运算符（===）进行判断，会导致对NaN的误判
- <---->Map和Set数据结构has方法
Map.has 用来查找键名  
Map.prototype.has(key)、WeakMap.prototype.has(key)、Reflect.has(target, propertyKey)。
Set.has用来查找值  
Set.prototype.has(value)、WeakSet.prototype.has(value)。

### 数组空位: [,,,]
- 数组的某一个位置没有任何值, !==undefined
- ES5处理：大多数情况下会忽略空位
    - forEach(), filter(), every() 和some()都会跳过空位。
    - map()会跳过空位，但会保留这个值
    - join()和toString()会将空位视为undefined，而undefined和null会被处理成空字符串。
- ES6处理：明确将空位转为undefined
由于空位的处理规则非常不统一，所以建议避免出现空位

# 函数
### 参数默认值
- ES5变通法： y = y || 'World';
    - 缺点：若y值可转为fause则无效
    - 再变通：if (typeof y === 'undefined') {y = 'World'}
- 默认值可为变量或表达式
- 可解构
    > 区别：function m1({x = 0, y = 0} = {}) {}
            function m2({x, y} = { x: 0, y: 0 }) {}

- 位置：
    - 函数的尾参数定义默认值才意义
    - 非尾参默认值实际不能省略

- length属性
    - 含义：该函数预期传入的参数个数
    - 等于函数的参数个数减去指定了默认值的参数个数
    - rest参数也不会计入length属性
- 参数作用域
    - 设置了参数的默认值后，函数进行声明初始化时参数会形成一个单独的作用域。等到初始化结束，这个作用域就会消失。
    - 参数不会读取函数内定义的同名变量！

- 应用：指定某参不得省略

### rest参数（...变量名）
- 主要用于函数定义，将将多余的参数放入数组中
- 代替在函数内再调用arguments
- 参数搭配的变量是一个数组，可使用所有数值方法
- rest 只能作为尾参

### 扩展运算符（...）
- 主要用于函数调用，将一个数组转为用逗号分隔的参数序列
- 扩展运算符内部调用的是数据结构的Iterator接口，因此只要具有Iterator接口的对象，都可以使用扩展运算符
- 应用  
    1. 替代apply方法
    ```
    // ES5的写法
    Math.max.apply(null, [14, 3, 77])
    // ES6的写法
    Math.max(...[14, 3, 77])
    // 等同于
    Math.max(14, 3, 77);
    ```
    2. 合并数组
    ```
    // ES5
    [1, 2].concat(more)
    // ES6
    [1, 2, ...more]
    ```
    3. 与解构赋值结合生成数组
    ```
    // ES5
    a = list[0], rest = list.slice(1)
    // ES6
    [a, ...rest] = list
    ```
    - 用于数组赋值，只能放在参数的最后一位，否则会报错
    4. 函数的返回多个值的调用
    ```
    var dateFields = readDateFields(database);
    var d = new Date(...dateFields);
    ```
    5. 将字符串转为数组
    ```
    [...'hello']
    // [ "h", "e", "l", "l", "o" ]
    ```
    - 能够正确识别32位的Unicode字符
    6. Iterator接口的对象可用扩展运算符转为数组
    ```
    // nodeList转数组
    var nodeList = document.querySelectorAll('div');
    var array = [...nodeList];
    ```
    7. Map和Set结构，Generator函数

### 严格模式
- 只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。
- 函数内部的严格模式，同时适用于函数体代码和函数参数代码。但是，函数执行的时候，先执行函数参数代码，然后再执行函数体代码。这样就有一个不合理的地方，只有从函数体代码之中，才能知道参数代码是否应该以严格模式执行，但是参数代码却应该先于函数体代码执行。
- 全局性的严格模式无限制
- 解法： 把函数包在一个无参数的立即执行函数里面
```
const doSomething = (function () {
  'use strict';
  return function(value = 42) {
    return value;
  };
}());
```
### name属性 =>函数名
- 如果将一个函数表达式的匿名函数赋值给一个变量，ES5 的name属性，会返回空字符串，而 ES6 的name属性会返回实际的函数名。
- Function构造函数返回的函数实例，name属性的值为anonymous
- bind返回的函数，name属性值会加上bound前缀。

### 箭头函数
- 写法：
    - 单参直接写：v=>v
    - 无参或多参包（）
    - 代码块多于一条语句：外包{}，用return返回
    - 直接返回对象需外包（）
        - 大括号被解释为代码块
- 特点：
    - 没有this / arguments / super / new.target, 指向外层代码块的对应变量
    - 也就不能用call()、apply()、bind()这些方法去改变this的指向
- 应用：简化回调函数
- 注意点：
    1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象
        => 箭头函数的this为固定值！
        - 箭头函数可以让setTimeout里面的this，绑定定义时所在的作用域，而不是指向运行时所在的作用域window
        - this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this
    2. 不可以当作构造函数(使用new命令)
        - 没有this，所以也就不能用作构造函数
    3. 不可以使用arguments对象。如果要用，可以用Rest参数代替。
    4. 不可以使用yield命令，因此箭头函数不能用作Generator函数
- 嵌套的箭头函数

### 绑定this(ES7)...

### 尾调用优化
...

### 尾逗号

# 对象的扩展
### 属性简洁表示法 {xxx}
- 允许在对象之中直接写变量和函数。这时属性名为变量名, 属性值为变量的值或函数表达式
- 应用：
    - 方法简写：
    ```
    var o = {
      method() {
        return "Hello!";
      }
    };
    // 等同于
    var o = {
      method: function() {
        return "Hello!";
      }
    };
    ```
    - 返回值简写： return {x,y}
    - commonJS模块输出：
    ```
    module.exports = { getItem, setItem, clear };
    // 等同于
    module.exports = {
      getItem: getItem,
      setItem: setItem,
      clear: clear
    };
    ```
    - 属性的赋值器（setter）和取值器（getter）
    - 如果某个方法的值是一个Generator函数，前面需要加上星号
    
### 属性名表达式
- 用字面量定义对象时，可把表达式放在方括号内作为对象的属性/方法名
```
let propKey = 'foo';
let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123，
  ['h' + 'ello']() {
    return 'hi';
  }
};
```
- 属性名表达式与简洁表示法不能同时使用，会报错！
- 属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串[object Object]！
```
const keyA = {a: 1};
const keyB = {b: 2};

const myObject = {
  [keyA]: 'valueA',
  [keyB]: 'valueB'
};

myObject // Object {[object Object]: "valueB"}
```

### 方法的name属性
- 返回相应key名
- 如果对象的方法使用了取值函数（getter）和存值函数（setter），则name属性不是在该方法上面，而是该方法的属性的描述对象的get和set属性上面，返回值是方法名前加上get和set
- 特殊情况：
    1. bind方法创造的函数，name属性返回bound加上原函数的名字；
    2. Function构造函数创造的函数，name属性返回anonymous
- 如果对象的方法是一个 Symbol 值，那么name属性返回的是这个 Symbol 值的描述

### Object.is()
- 功能：修复===缺陷：1. +0不等于-0； 2. NaN等于自身
- 并非只适用于对象

### Object.assign(target,source1,source2,...) 对象合并
- 将源对象（source）的所有可枚举属性，复制到目标对象（target）
- 属性同名时后覆盖前
- 合并前会先将非对象参数项都转为对象，基本包装类型转外包类型，数组不会转换
- 拷贝的特性：
    1. 只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（enumerable: false）
    2. 浅拷贝，而不是深拷贝。即如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用
- 如需执行深拷贝，可用Lodash的_.defaultsDeep方法
- 仅参1时：
    - 会转为对象 =>可用于转换类型
    - target为undefined或null时会报错
- sourceX的处理：    
    - 如无法转为对象直接跳过不报错
    - 实际上基本类型转换只有字符串会以数组形式，拷贝入目标对象，数组、布尔无效（其包装类型不可枚举）
- 应用：  
    1. 为对象添加属性
    ```
    class Point {
      constructor(x, y) {
        Object.assign(this, {x, y});
      }
    }
    ```
    2. 为对象添加方法
    ```
    Object.assign(SomeClass.prototype, {
      someMethod(arg1, arg2) {
        ···
      },
      anotherMethod() {
        ···
      }
    });
    
    // 等同于下面的写法
    SomeClass.prototype.someMethod = function (arg1, arg2) {
      ···
    };
    SomeClass.prototype.anotherMethod = function () {
      ···
    };
    ```
    3. 克隆对象
    ```
    function clone(origin) {
      return Object.assign({}, origin);
    }
    //如果想要保持继承链，可以采用下面的代码:
    function clone(origin) {
      let originProto = Object.getPrototypeOf(origin);
      return Object.assign(Object.create(originProto), origin);
    }
    ```
    4. 合并对象 
    ```
    //不改变原target对象的方法
    const merge = (...sources) => Object.assign({}, ...sources);
    ```
    5. 为属性指定默认值
    ```
    //仅适用于属性为基本类型时，若为对象将导致DEFAULTS对象的该属性不起作用
    const DEFAULTS = {
      logLevel: 0,
      outputFormat: 'html'
    };
    function processContent(options) {
      options = Object.assign({}, DEFAULTS, options);
    }
    ```
### 属性的可枚举性
- 描述对象（Descriptor）:对象的每个属性都有，用来控制该属性的行为。可通过Object.getOwnPropertyDescriptor（obj,attr）获取
- Descriptor.enumerable属性：可枚举性
- 忽略enumerable为false的属性的操作。
    1. for...in循环：只遍历对象自身的和**继承的**可枚举的属性
    2. Object.keys()：返回对象自身的所有可枚举的属性的键名
    3. JSON.stringify()：只串行化对象自身的可枚举的属性
    4. (ES6)Object.assign()
    - 只有for...in会返回继承的属性---操作中引入继承的属性会让问题复杂化，我们一般只关心对象自身的属性==> **尽量不要用for...in循环，而用Object.keys()代替！**
- 不可枚举属性示例：对象原型的toString方法/ 数组的length属性/ ES6规定所有Class的原型的方法都是不可枚举的
    
### 属性的遍历
1. for...in: 遍历对象自身的和继承的可枚举属性（不含Symbol属性）
2. Object.keys(obj)=>[对象自身的（不含继承的）所有可枚举属性（不含Symbol属性）]
3. Object.getOwnPropertyNames(obj)=>[对象自身的所有属性（不含Symbol属性，但是包括不可枚举属性）]
4. Object.getOwnPropertySymbols(obj)=>[所有Symbol属性]
5. Reflect.ownKeys(obj)=>[对象自身的所有属性(不管是属性名是Symbol或字符串，也不管是否可枚举)]
6. Object.values(obj) =>[对象自身的（不含继承的）所有可遍历属性的键值(不含属性名为 Symbol 值的属性)]
7. Object.entries(obj) => [对象自身的（不含继承的）所以可遍历属性的键值对(不含属性名为 Symbol 值的属性)]

- 以上属性如参非obj,会先转obj

- 遍历次序规则：
    1. 遍历所有属性名为数值的属性，按照数字排序。
    2. 遍历所有属性名为字符串的属性，按照生成时间排序。
    3. 遍历所有属性名为Symbol值的属性，按照生成时间排序。

### prototype操作
- \__proto__属性: 读取或设置当前对象的prototype对象
    - \__proto__调用的是Object.prototype.\__proto__
    - 本质上为内部属性，因浏览器广泛支持才加入ES6
    - ES6规定只有浏览器必须部署这个属性，其他运行环境非必需   
        => 尽量不用，用以下方法代替
    
- Object.setPrototypeOf(obj, proto) 
    - 设置obj的prototype对象为proto，功能同\__proto__

- Object.getPrototypeOf(obj)
    - 读取obj的prototype对象

- Object.create(prototype,descriptors) --自加
    - 创建一个具有指定原型且可选择性地包含指定属性的对象
    - descriptors:value / writable / enumerable / configurable 特性

### 扩展运算符for对象
- 解构赋值：
    - 从一个对象取值，将所有可遍历的、但尚未被读取的属性，分配到指定的对象上面
    - 须为末参
    - 解构赋值的拷贝为浅拷贝，即若某键值是引用类型的值，解构赋值拷贝的是该值的引用，而非副本
    - 解构赋值不会拷贝继承自原型对象的属性
    - 应用：扩展某个函数的参数，引入其他操作
- 扩展运算符
    - 取出对象所有可遍历对象，拷贝到当前对象中
    - 实际等同于使用Object.assign()
    ```
    let aWithOverrides = { ...a, x: 1, y: 2 };
    // 等同于
    let aWithOverrides = { ...a, ...{ x: 1, y: 2 } };
    // 等同于
    let x = 1, y = 2, aWithOverrides = { ...a, x, y };
    // 等同于
    let aWithOverrides = Object.assign({}, a, { x: 1, y: 2 });
    ```    
    - 不须为末参
    - 同名属性后覆盖前
        - 应用：修改现有对象部分的部分属性——自定义的属性放在扩展运算符后面
        ```
        let newVersion = {
          ...previousVersion,
          name: 'New Name' // Override the name property
        };
        ```
        - 应用：设置新对象的默认属性值——自定义属性放在扩展运算符前面
        ```
        let aWithDefaults = { x: 1, y: 2, ...a };
        ```
    - 扩展运算符的参数对象之中如果有取值函数get，这个函数是会执行的

- Object.getOwnPropertyDescriptors(obj) => obj所有自身属性的描述对象
    - ES5有Object.getOwnPropertyDescriptor(obj, attr) 返回某项
    - 作用：
        - 解决Object.assign()无法正确拷贝get属性和set属性的问题
        - 配合Object.create方法，将对象属性克隆到一个新对象
        ```
        Object.create(Object.getPrototypeOf(obj),Object.getOwnPropertyDescriptors(obj))
        ```


# Symbol
- 第7种数据类型，类似字符串的数据
- 生成的数据都独一无二，可保证不会与其他变量名产生冲突
- 通过Symbol函数生成，但没有new命令
- 可作为对象属性名
- 不是对象，所以不能添加属性
- Symbol(str):可接受一个字符串作为参数，表示对Symbol实例的描述
- str只表示对当前Symbol值的描述，同参数的Symbol函数的返回值不相等
- Symbol(obj):调用obj的toString方法，转为字符串再生成Symbol值
- 不能直接与其他类型进行运算
- 可转为串或布尔，但不能转数值
    ```
    Symbol('xxx').toString() => 'Symbol(xxx)';
    !Symbol('xxx') => false
    ```
...

# Set & Map 数据结构
## Set
- 类似数组，但成员唯一不重复
- typeof new Set() => object
- new Set(可用一数组或类数组对象为参来初始化，数组中重复项会忽略)
- 内部相同判断：Same-value equality, 类似===,但NaN等于自身，且两个对象总是不相等的：{}!={}
- Array.from可将Set转数组
- 应用：
- 属性：
    - constructor 构造函数 
    - size 成员个数
- 方法:
    - 操作方法:
        - add(value) => Set本身
        - delete(value) => T/F 删除是否成功
        - has(value) => T/F 
        - clear() 清空所有成员
    - 遍历方法：
        - Set的遍历顺序就是插入顺序——应用：使用Set保存一个回调函数列表，调用时就能保证按照添加顺序调用。
        - 扩展运算符适用：let arr = [...set];
        - keys() / value(): Set结构没有键名只有键值, 两者完全一致
        - entries()
        - set结构默认遍历器为其values方法，所以也可直接遍历：for(let x of set/set.values())
        - forEach(fn(value,key,set){},context)
    - 应用：
        - 扩展运算符：let arr = [...set];
        - 数组去重新法：[...new Set(array)] 或 Array.from(new Set(array))
        - 应用数组方法：
        ```
        set = new Set([...set].map(x => x * 2));
        set = new Set([...set].filter(x => (x % 2) == 0));
        ```
        - 在遍历操作中改变原来的Set结构没有直接的方法--间接法:如上或用Array.from()
        - 实现集合运算：
        ```
        let a = new Set([1, 2, 3]);
        let b = new Set([4, 3, 2]);
        
        // 并集
        let union = new Set([...a, ...b]);
        // Set {1, 2, 3, 4}
        // 交集
        let intersect = new Set([...a].filter(x => b.has(x)));
        // set {2, 3}
        // 差集
        let difference = new Set([...a].filter(x => !b.has(x)));
        // Set {1}
        ```
## WeakSet        
...

## Map
- 类似于对象，键值对集合，但键的类型可为各种类型的值（包括对象）
- Object结构提供了“字符串—值”的对应，Map结构提供了“值—值”的对应
- new Map([[键名, 键值],...]) 接受一个数组为参，成员为数个表示键值对的数组,内部原理为：
```
var items = [
  ['name', '张三'],
  ['title', 'Author']
];
var map = new Map();
items.forEach(([key, value]) => map.set(key, value));
```
- 同键名判断：
    - 同键重复赋值，后值将覆盖前值
    - 基本类型的相同键按Same-value equality判断
    - 对于对象键名，只有对同一个对象的引用，Map结构才将其视为同一个键，写法相同的结构实际不同值，内存地址不一样
    ```
    var map = new Map();
    map.set(['a'], 555);
    map.get(['a']) // undefined
    ```
    - Map的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。解决了同名属性碰撞（clash）的问题, 我们扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名
- 属性：
    - size: 返回Map结构的成员总数
- 方法：
    - 操作方法：
        - set(key,value) => Map本身
            - 可链式写法
        - get(key）=> 对应键值
        - has(key) => T/F
        - delete(key) => T/F
        - clear()
    - 遍历方法：
        - 遍历顺序即插入顺序
        - keys()
        - values()
        - entries()
        - set结构默认遍历器为其entries方法，所以也可直接遍历：  
            for(let x of map/map.entries())
        - forEach(fn(value,key,map){},context)
        - 扩展运算符： Map转[]: [...map]
        - 应用数组方法：
        ```
        let map0 = new Map()
          .set(1, 'a')
          .set(2, 'b')
          .set(3, 'c');
        
        let map1 = new Map(
          [...map0].filter(([k, v]) => k < 3)
        );
        // 产生Map结构 {1 => 'a', 2 => 'b'}
        
        let map2 = new Map(
          [...map0].map(([k, v]) => [k * 2, '_' + v])
            );
        // 产生Map结构 {2 => '_a', 4 => '_b', 6 => '_c'}
        ```
- 与其他类型的转换 ...

## WeakMap
...


# Proxy
...
# Reflect
...

# Promise对象
- 容器，保存着某个未来才会结束的事件（通常是一异步操作）的结果；
- 可以获取异步操作的消息的对象
- 特点:
    - 对象状态不受外界影响
        - 3状态：
            - Pending（进行中）
            - Resolved（已完成，又称 Fulfilled）
            - Rejected（已失败）
        - 只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态
    - 一旦状态改变，就不会再变，任何时候都可以得到这个结果 
        - Promise对象的状态改变，只有两种可能：从Pending变为Resolved和从Pending变为Rejected
        <--->区别事件（Event）:如果你错过了它，再去监听，是得不到相同结果的
- 使用Promise对象，可将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数 
- 缺点：
    - 无法取消
    - 如不设回调函数，Promise内部抛出的错误，不会反应到外部
    - 处于Pending状态时无法得知目前进行到哪阶段
- 如果某些事件不断地反复发生，一般使用 stream模式是比部署Promise更好的选择

### 基本用法
1. 生成Promise实例
```
var promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```
- Promise对象为构造函数
- 参为函数，函数参为resolve和reject —— JS自带函数
- resolve(value)函数：
    - 将Promise对象的状态从Pending变为Resolved
    - 异步操作成功时调用，并将异步操作结果作为参传递出去
    - 参为一般值或另一个Promise实例
- reject(error)函数：
    - 将Promise对象的状态从Pending变为Rejected
    - 异步操作失败时调用，并将报的错误作为参传递出去
    - 参常为Error对象的实例
2. 用then指定Resolved和Reject状态回调函数 
```
promise.then(function(value){},function(error))
```
- then方法
    - 接受二回调函数为参。参1为状态变为Resolved时调用，参2（可选）为状态变为Reject时调用。
    - 两个函数都接受Promise对象传出的值作为参数
- Promise新建后就会立即执行
### Promise.prototype
1. .then()
    - then方法返回的是一个新的Promise实例，因此可以采用链式写法，即then().then()...
    - 后面的then()始终会等前面的promise为resolved才执行
2. .catch()
    - .then(null, rejection)的别名,用于发生错误时的回调
    - reject方法的作用等同于抛出错误：
    ```
    var promise = new Promise(function(resolve, reject) {
      throw new Error('test');
    });
    //等同于
    var promise = new Promise(function(resolve, reject) {
      reject(new Error('test'));
    });
    ```
    - 若Promise状态已经变成Resolved，再抛出错误是无效的
    - Promise对象的错误具有“冒泡”性质，会一直向后（不向前）传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获--因为每个then都会返回新的Promise,冒泡性质使catch能写在then后面
    ```
    getJSON("/post/1.json").then(function(post) {
      return getJSON(post.commentURL);
    }).then(function(comments) {
      // some code
    }).catch(function(error) {
      // 处理前面三个Promise产生的错误
    });
    ```
    - 一般来说，不要在then方法里面定义Reject状态的回调函数（即then的第二个参数），总是使用catch方法！—— 更接近同步的写法（try/catch）
    - 区别try/catch代码块：如果没有使用catch方法指定错误处理的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应
        - Chrome除外：会抛出错误！
        - 特殊情况：
        ```
        //此情况错误发生时Promise的函数体已经运行结束了，此错误是在Promise函数体外抛出的
        var promise = new Promise(function(resolve, reject) {
          resolve('ok');
          setTimeout(function() { throw new Error('test') }, 0)
        });
        promise.then(function(value) { console.log(value) });
        // ok
        // Uncaught Error: test
        ```
    - catch方法返回的还是一个 Promise 对象，因此后面还可以接着调用then方法！
    - catch返回的Promise对象还可能再抛出错误
    - Node 有一个unhandledRejection事件，专门监听未捕获的reject错误

### Promise.all([p1,p2,...])
- 将多个Promise实例包装成一个新的Promise实例
- 参一般为array，其成员为Promise实例；如非Promise则调用Promise.resolve()转为Promise
- 参非数组但有Iterator也行
- p综合状态：
    - 参均resolved => p:resolved => p的回调函数(参返回值组成数组)
    - 参有rejucted => p:rejucted => p的回调函数(首个rejucted实例返回值)

### Promise.race([p1,p2,...])
- 将多个Promise实例包装成一个新的Promise实例
- 参一般为array，其成员为Promise实例；如非Promise则调用Promise.resolve()转为Promise
- p综合状态：参之中有一实例率先改变状态，p的状态就跟着改变。p的回调函数(率先改变的Promise实例的返回值)

### Promise.resolve()/Promise.reject()
- 将现有对象转为Promise对象
...

### 非ES6的附加方法（需自写代码增加）
- .done() 
    - Promise内部的错误不会冒泡到全局==>Promise对象的回调链，不管以then方法或catch方法结尾，要是最后一个方法抛出错误，都有可能无法捕捉到
    - done方法放在回调链的尾端，保证捕捉到任何可能出现的错误
    - 无参时只用来捕获错误，不执行其他操作
    ```
    Promise.prototype.done = function (onFulfilled, onRejected) {
        this.then(onFulfilled, onRejected)
        .catch(function (reason) {
          // 抛出一个全局错误
          setTimeout(() => { throw reason }, 0);
        });
    };
    ```
- .finally()
    - 指定不管Promise对象最后状态如何都会执行的操作
    - 与done方法的区别：它接受一个普通的回调函数作为参数，该函数不管怎样都必须执行。
    ```
    Promise.prototype.finally = function (callback) {
      let P = this.constructor;
      return this.then(
        value  => P.resolve(callback()).then(() => value),
        reason => P.resolve(callback()).then(() => { throw reason })
      );
    };
    ```

### 应用
- 加载图片
- 与Generator函数结合
- Promise.try()

# Iterator和for...of循环
### Iterator
- 一种接口，为各种不同的数据结构提供统一的访问机制，即for...of循环
- 一种数据结构只要部署了Iterator接口，我们就称这种数据结构是”可遍历的“（iterable）
- 本质是一个指针对象
- 流程：
    1. 创建指针对象，指向数据起始位置
    2. 一次调用指针的next方法，使指针移动
    3. 每次调用next方法 => 当前成员的value和done属性
        - value: 当前成员的值
        - done(T/F): 遍历是否结束,即是否还有必要再一次调用next方法
- Iterator只是把接口规格加到数据结构之上，即遍历器与所遍历数据结构实际上是分开的
- 完全可以写出没有对应数据结构的遍历器对象，或者说用遍历器对象模拟出数据结构
- 重写覆盖原生的Symbol.iterator方法，可修改遍历器行为
- Symbol.iterator属性
    - 某些数据结构原生具备Iterator接口，因为部署了Symbol.iterator属性
    - Symbol对象的iterator属性，这是一个预定义好的、类型为Symbol的特殊值，所以要放在方括号内
    - 值为当前数据结构默认的遍历器生成函数，执行这个函数，就会返回一个遍历器（arr[Symbol.iterator]()）
- 原生具备Iterator接口的三类数据结构：数组、某些类似数组的对象、Set和Map结构
- 本质上遍历器是一种线性处理，对于任何非线性的数据结构(如对象)都需自行部署遍历器接口，做线性转换
- 字符串的Iterator也默认带
- 其他数据结构如需遍历需自行在Symbol.iterator属性上部署Interator接口
    - 对象加iterator
    ```
    let obj = {
      data: [ 'hello', 'world' ],
      [Symbol.iterator]() {
        const self = this;
        let index = 0;
        return {
          next() {
            if (index < self.data.length) {
              return {
                value: self.data[index++],
                done: false
              };
            } else {
              return { value: undefined, done: true };
            }
          }
        };
      }
    };
    //调用数组的Symbol.iterator
    const arr = ['red', 'green', 'blue'];
    const obj = {};
    obj[Symbol.iterator] = arr[Symbol.iterator].bind(arr);
    
    for(let v of obj) {
      console.log(v); // red green blue
    }
    ```
    - 类数组对象加iterator——直接引用数组的Iterator接口
    ```
    1. 原生类数组对象
    NodeList.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
    // 或者
    NodeList.prototype[Symbol.iterator] = [][Symbol.iterator];
    [...document.querySelectorAll('div')] // 可以执行了
    2. 自定义类数组对象
    let iterable = {
      0: 'a',
      1: 'b',
      2: 'c',
      length: 3,
      [Symbol.iterator]: Array.prototype[Symbol.iterator]
    };
    for (let item of iterable) {
      console.log(item); // 'a', 'b', 'c'
    }
    ```
### 使用Iterator接口的场合
- for... of循环
- while循环--末尾加.next()
- 数组和set解构赋值,默认调用
- 扩展运算符，默认调用
    - 任何部署了Iterator接口的数据结构，都可使用扩展运算符，将其转为数组。
- yield* 可遍历结构
- 任何接受数组为参的参合都调用
    - for...of
    - Array.from()
    - Map(), Set(), WeakMap(), WeakSet()（比如new Map([['a',1],['b',2]])）
    - Promise.all()
    - Promise.race()

### 结合Generator函数
Symbol.iterator方法的最简单实现是使用Generator函数

### 遍历器方法：
- next()
- return()(可选)：
    - for...of循环提前退出（通常是因为出错，或者有break语句或continue语句）就会调用return方法。
    - 必须返回一个对象
- throw()(可选)：用于配合Generator函数使用，一般的遍历器对象用不到这个方法

### for...of循环
- 遍历所有数据结构的统一的方法
- 部署了Symbol.iterator属性，具有iterator接口就可用
- for...of循环内部调用的是数据结构的Symbol.iterator方法
- 使用范围：
    - 数组
        - 可代替forEach方法
        - vs for...in
            - for...in循环读取键名，for...of循环读取键值
            - for...of循环调用数组, 只返回具有数字索引的属性
            ```
            let arr = [3, 5, 7];
            arr.foo = 'hello';
            for (let i in arr) {
              console.log(i); // "0", "1", "2", "foo"
            }
            for (let i of arr) {
              console.log(i); //  "3", "5", "7"
            }
            ```
    - Set 和 Map 
        - 遍历的顺序是按照各个成员被添加进数据结构的顺序
        - Set遍历返回的是一个值，Map结构遍历返回一数组，该数组的两个成员分别为当前Map成员的键名和键值。
    - 某些类似数组的对象
        - （比如arguments对象、DOM NodeList 对象）
        - 不具有iterator接口的可使用Array.from方法转为数组再遍历
    - Generator 对象
    - 字符串
    - 对象：
        - 普通对象不能有for...of，但可用for...in
        - 遍历法：
            - 使用Object.keys(obj)生成数组再遍历
            - 部署iterator接口，简便法：借用数组的symbol.iterator属性
            ```
            // jQuery对象使遍历
            jQuery.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
            ```
            - 使用Generator函数包装
- 遍历方法比较
    - for :写法麻烦
    - forEach:for数组, 无法用break或return跳出
    - for...in:用于数组有缺点：
        - 数组的键名是数字，但是for...in循环是以字符串作为键名: “0”、“1”等
        - 不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键
        - 某些情况下顺序不可控
    - for...of: 优点：
        - 简洁，但是没有for...in那些缺点。
        - 可与break、continue和return配合使用。
        - 遍历所有数据结构的统一接口
        
# Generator函数
- 异步编程解决方案
- 状态机，封装了多个内部状态
- 遍历器对象生成函数，返回一个遍历器对象，可遍历 Generator 函数内部的每一个状态。
- 写法：
    - function与函数名之间有一个星号
    - 函数体内部使用yield语句，定义不同的内部状态
- 调用：
    - 写法同普通函数
    - 调用函数后不执行，返回一个指向内部状态的指针对象（遍历器对象）
    - 调用遍历器对象的next方法，使得指针移向下一个状态，返回{value，done}
    - Generator函数分段执行，yield语句是暂停执行的标记，而next方法可以恢复执行。
- yield语句
    - next运行逻辑：
        - 遇到就暂停, 将yield后面表达式的值，作为返回的对象的value值
        - 直到return为止，并将return后面表达式的值，作为返回的对象的value值
        - 没有return语句，则返回value=undefined
        - 等于为JavaScript提供了手动的“惰性求值”功能
    - yield vs return
        - 同：返回后面的值
        - 异：
            - 具备位置记忆功能
            - 函数内可多次执行
            - 可返回一系列值
            - 不能用于普通函数
    - Generator函数可于普通函数 generator.next()==fn()
    - yield用在一个表达式中必须放在圆括号里面
    - Generator是遍历器生成函数，因此可以把Generator赋值给对象的Symbol.iterator属性，从而使得该对象具有Iterator接口
    - Generator函数执行后，返回一个遍历器对象。该对象本身也具有Symbol.iterator属性，该属性也是一个遍历器对象生成函数，执行后返回自身

### next的参
- yield本身没有返回值（返回undefinded）,但next(xx)的参将当成上一次yeild的返回值
- 上一次，过第一次调用next()不可带参
- 作用：在Generator运行的不同阶段，向函数体内注入值，从而调整函数行为

### for...of循环
- 可自动遍历Generator函数时生成的Iterator对象，且此时不再需要调用next方法。
- 一旦next返回的done=true，循环即终止，且丢弃该返回值
- 利用for...of循环及Generator函数，可以写出遍历任意对象（object）的方法
```
function* objectEntries(obj) {
  let propKeys = Reflect.ownKeys(obj);

  for (let propKey of propKeys) {
    yield [propKey, obj[propKey]];
  }
}

let jane = { first: 'Jane', last: 'Doe' };

for (let [key, value] of objectEntries(jane)) {
  console.log(`${key}: ${value}`);
}
// first: Jane
// last: Doe
```
- 扩展运算符、解构赋值和Array.from方法内部也调用遍历器接口。故都可将Generator函数返回的Iterator对象作为参数

### Generator.prototype.throw()
- Generator返回的遍历器对象，都有一个throw方法，可以在函数体外抛出错误，然后在Generator函数体内捕获。
- throw方法可接受一参数，其会被catch语句接收，建议抛出Error对象的实例
```
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log(e);
  }
};
var i = g();
i.next();
i.throw(new Error('出错了！'));
// Error: 出错了！(…)
```
- 区别全局throw：其只能被Generator外的catch捕获
- 如Generator内未部署try-catch，则throw抛的错将被外部的try-catch捕获
- throw方法被捕获以后，会附带执行下一条yield语句。也就是说，会附带执行一次next方法


.......
.......


# Class
- 原构造函数+原型链写法的语法糖
- 写法：
```
class Xxxx{
    //对应ES5的的构造函数
    constructor(a,b){
        this.a = a
    }
    //对应ES5的原型链方法
    //方法间无逗号！！
    toString(){
    }
}
```
- 性质：基本同ES5
    - typeof Xxx => function
    - Xxx === Xxx.prototype.Xxx => T
    - Xxx.方法 === Xxx.prototype.方法 所有方法（包括contructor）都定义在prototype上
    - 在prototype对象上添加方法等同于在类上添加
    ```
    //prototype也是对象，可用Object.assign添加多个方法
    Object.assign(Point.prototype, {
      toString(){},
      toValue(){}
    });
    ```
    - 区别：
        - 类的内部所有定义的方法，都是不可枚举的（non-enumerable）
        - Class不存在变量提升，必须定义在前
            - 原因：为了保证继承，子类需在父类之后定义：class Bar extends Foo{}
- 类的属性名可用表达式[xxx](){}
- 类和模块的内部默认严格模式
- xxx.name => xxx

### constructor方法
- 类的默认方法
- 未显式定义时，默认添加一空的constructor方法
- 通过new命令生成对象实例时，自动调用该方法
- 返回实例对象（this）,完全可在其中return指定另一个对象

### 实例
- new Xxx()，无new无法调用
- 与ES5一样，实例的属性除非显式定义在其本身（即定义在this对象上），否则都是定义在原型上（即定义在class上）
- 与ES5一样，类的所有实例共享一个原型对象
- 可以通过实例的__proto__属性为Class添加方法，这样做会改写原型

### Class表达式
```
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
}
//该类的名字是MyClass而不是Me，Me只在Class的内部代码可用，指代当前类,可省略
```
- 立即执行Class:
```
let person = new class {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
}('张三');
person.sayName(); // "张三"
```
### 定义私有方法
1. 在命名上加以区别：方法前加_ 
    - 不保险，仍可能被调用
2. 将私有方法移出模块，因为模块内部的所有方法都是对外可见的。
```
class Widget {
  foo (baz) {
    bar.call(this, baz);
  }
  // ...
}
function bar(baz) {
  return this.snaf = baz;
}
```
3. 将私有方法的名字命名为一个Symbol值

### this指向
- 默认指向类的实例
- 在类外单独使用时可能出错
- 解法：
1. 在构造方法中绑定this
```
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }
  // ...
}
```
2. 使用箭头函数
class Logger {
  constructor() {
    this.printName = (name = 'there') => {
      this.print(`Hello ${name}`);
    };
  }
  // ...
}
3. Proxy

## Class继承
```
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }
  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```
- 通过extends关键字实现继承
- 没部署任何代码则等于复制了一个Point类
- super表示父类的构造函数，用来新建父类的this对象
- 子类必须在constructor方法中调用super方法，因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法子类就得不到this对象
- ES5的继承实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply(this)）。
- ES6的继承机制实质是先创造父类的实例对象this（所以必须先调用super方法），然后再用子类的构造函数修改this
- 同ES5，子类实例同时是子类和父类的实例

- Class同时有prototype属性和__proto__属性，因此同时存在两条继承链
    - 子类的__proto__属性，表示构造函数的继承，总是指向父类。
    - 子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性

### 继承目标
- extends后面可跟多类型的值，只要是一个有prototype属性的函数，就能被B继承
- 由于函数都有prototype属性（除了Function.prototype函数），因此A可以是任意函数
- 特殊情况：
    1. class A extends Object{}
    2. class A {}：A作为一个基类，就是一个普通函数，所以直接继承Funciton.prototype。但是，A调用后返回一个空对象（即Object实例），所以A.prototype.__proto__指向构造函数（Object）的prototype属性 
    3. class A extends null {}:A也是一个普通函数，所以直接继承Funciton.prototype。但A调用后返回的对象不继承任何方法，所以它的__proto__指向Function.prototype
    
### Object.getPrototypeOf
- 用来从子类上获取父类
- 可用来判断一个类是否继承了另一个类：
    Object.getPrototypeOf(ColorPoint) === Point// true

### super
- 当作函数使用
    - 代表父类的构造函数
    - 返回的是子类的实例，即super内部的this指的是子类，因此super()在这里相当于A.prototype.constructor.call(this)
    - super()只能用在子类的构造函数之中

- 当作对象使用
    - 指向父类的原型对象
    - 定义在父类实例上的方法或属性无法通过super调用的
    - 通过super调用父类的方法时，super会绑定子类的this
    - 如果通过super对某个属性赋值，这时super就是this，赋值的属性会变成子类实例的属性
- 使用super的时候，必须显式指定是作为函数、还是作为对象使用，否则会报错
- 对象总是继承其他对象的，所以可以在任意一个对象中使用super
```
var obj = {
  toString() {
    return "MyObject: " + super.toString();
  }
};
obj.toString(); // MyObject: [object Object]
```
### 实例的__proto__属性
- 子类实例的__proto__属性的__proto__属性，指向父类实例的__proto__属性。也就是说，子类的原型的原型，是父类的原型

## 原生构造函数
- Boolean()Number()String()Array()Date()Function()RegExp()Error()Object()
- ES5不能继承：因为ES5是先新建子类的实例对象this，再将父类的属性添加到子类上，由于父类的内部属性无法获取，导致无法继承原生的构造函数。原生构造函数会忽略apply方法传入的this，也就是说，原生构造函数的this无法绑定，导致拿不到内部属性
- ES6允许继承：因为ES6是先新建父类的实例对象this，然后再用子类的构造函数修饰this，使得父类的所有行为都可以继承
...
...
...

# Module
- 可取代CommonJS和AMD
- 设计思想：尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量
- commonJS的模块就是对象(module.exports属性)，输入时必须查找对象属性；其require方法实际上是"运行时加载"，只有运行时才能得到这个对象，导致完全没办法在编译时做"静态优化"
- ES6模块非对象，需通过export命令显式指定输出的代码，再通过import命令输入
- 导致了没法引用 ES6 模块本身，因为它不是对象
- 优点：
    - 为"编译时加载"或者静态加载，module.exports属性即可在编译时就完成模块加载，效率比CommonJS模块的加载方式高
    - 不再需要UMD模块格式了，将来服务器和浏览器都会支持 ES6 模块格式。
    - 将来浏览器的新 API 就能用模块格式提供，不再必须做成全局变量或者navigator对象的属性。
    - 不再需要对象作为命名空间，未来这些功能可以通过模块提供。
- 自动采用严格模式
- 不宜在顶层代码使用this,其指向undefined——严格模式特征

> 严格模式主要限制:
    - 变量必须声明后再使用
    - 函数的参数不能有同名属性，否则报错
    - 不能使用with语句
    - 不能对只读属性赋值，否则报错
    - 不能使用前缀0表示八进制数，否则报错
    - 不能删除不可删除的属性，否则报错
    - 不能删除变量delete prop，会报错，只能删除属性delete global[prop]
    - eval不会在它的外层作用域引入变量
    - eval和arguments不能被重新赋值
    - arguments不会自动反映函数参数的变化
    - 不能使用arguments.callee
    - 不能使用arguments.caller
    - 禁止this指向全局对象
    - 不能使用fn.caller和fn.arguments获取函数调用的堆栈
    - 增加了保留字（比如protected、static和interface）

## export
- 用于规定模块的对外接口
- 未export的变量、函数、类无法外部获取
- 推荐写法：export {firstName, lastName, year} 写在底部
- 默认下输出变量即定义的名字，也可用as充命名
- 规定的是对外的接口，必须与模块内部的变量建立一一对应关系
```
// 写法一
export var m = 1;
// 写法二
var m = 1;
export {m};
// 写法三
var n = 1;
export {n as m};
```
- 输出的接口与其对应的值是动态绑定，即可取到模块内部实时的值
<---->CommonJS模块输出的是值的缓存，不存在动态更新, 除非写成一个函数

- export及import都必须放在顶层对象，不可放在块级作用域中；可放在顶层任意位置


## import
- 可用as重命名： import { lastName as surname } from './profile';
- from后路径可绝可相，.js可省略
- 也可通过配置文件指定路径，此后可只在from后加文件名
- 具有提升效果，会提升到头部首先执行
- 是静态执行，所以不能使用表达式和变量等只有在运行时才能得到结果的语法结构
- 也可直接在import后加模块名 ：import 'lodash'
- 多次重复执行同一句import语句，只会执行一次

### 模块整体加载
- 用星号（*）指定一个对象，所有输出值都加载在这个对象上面
```
import * as circle from './circle';
console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```
- 整体加载所在的那个对象（上例是circle），应可静态分析，不允许运行时改变(只读)

## export default
- 指定默认输出
- 对defalut，import是可直接重命名
- 一个模块只能有一个默认输出，因此模块内export default命令只能使用一次，且输出变量不用加{}
==> import命令后面也不用加{}，因为只可能对应一个方法
```
// 第一组
export default function crc32() { // 输出
  // ...
}
import crc32 from 'crc32'; // 输入
// 第二组
export function crc32() { // 输出
  // ...
};
import {crc32} from 'crc32'; // 输入
```
- 本质上是输出一个叫做default的变量或方法，所以可任意重命名
```
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as xxx } from 'modules';
// 等同于
// import xxx from 'modules';
```
- 后面不能跟变量声明语句:
```
// 正确
export var a = 1;
// 正确, export default a的含义是将变量a的值赋给变量default
var a = 1;
export default a;
// 错误
export default var a = 1;
```
- 对外接口即defalut, 故可直接加常/量
```
// 正确
export default 42;
// 报错
export 42;
```
- 在一条import语句中，同时输入默认方法和其他变量: import _, { each } from 'lodash';
- imoprt *命令会忽略export的模块的default

## 复合写法
- 在一个模块之中，先输入后输出同一个模块，import语句可以与export语句写在一起
- 用途：用来合并多个模块的媒介模块
```
export { foo, bar } from 'my_module';
// 等同于
import { foo, bar } from 'my_module';
export { foo, bar };

// 接口改名
export { foo as myFoo } from 'my_module';
// 整体输出
export * from 'my_module';
// 默认接口
export { default } from 'foo';
//具名接口改默认接口
export { es6 as default } from './someModule';
// 等同于
import { es6 } from './someModule';
export default es6;
// 默认接口改具名接口
export { default as es6 } from './someModule';
```

## 模块间的继承
...

> `<scritp>`属性defer与async的区别是：前者要等到整个页面正常渲染结束，才会执行；后者一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。一句话，defer是“渲染完再执行”，async是“下载完就执行”。另外，如果有多个defer脚本，会按照它们在页面出现的顺序加载，而多个async脚本是不能保证加载顺序的

## 浏览器加载规则
- 浏览器加载ES6模块：`<script type="module" src="foo.js"></script>`
- 对带有type="module"的`<script>`都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的defer属性
- 还可再打开async属性，这时只要加载完成，渲染引擎就会中断渲染立即执行。执行完成后，再恢复渲染
- ES6模块也允许用内嵌`<script>`
- 注意点：
    - 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见
    - 模块脚本自动采用严格模式，不管有没有声明use strict。
    - 模块之中，可以使用import命令加载其他模块（.js后缀不可省略，需要提供绝对 URL 或相对 URL），也可以使用export命令输出对外接口
    - 模块之中，顶层的this关键字返回undefined，而不是指向window。也就是说，在模块顶层使用this关键字，是无意义的。
    - 同一个模块如果加载多次，将只执行一次
- 利用顶层的this等于undefined这个语法点，可以侦测当前代码是否在 ES6 模块之中
```
const isNotModuleScript = this !== undefined;
```

## ES6 模块 vs CommonJS 模块
- CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口

## Node加载规则
- 有自己的 CommonJS 模块格式，与 ES6 模块格式是不兼容的
- 目前的解决方案是，将两者分开，ES6 模块和 CommonJS 采用各自的加载方案
- 在静态分析阶段，一个模块脚本只要有一行import或export语句，Node 就会认为该脚本为 ES6 模块，否则就为 CommonJS 模块。如果不输出任何接口，但是希望被 Node 认为是 ES6 模块，可以在脚本中加一行语句：
export {} ——不输出任何接口的 ES6 标准写法，而非输出一个空对象
- 查找文件规则：
    - 如何不指定绝对路径，Node 加载 ES6 模块会依次寻找以下脚本，与require()的规则一致
    ```
    import './foo';
    // 依次寻找
    //   ./foo.js
    //   ./foo/package.json
    //   ./foo/index.js
    
    import 'baz';
    // 依次寻找
    //   ./node_modules/baz.js
    //   ./node_modules/baz/package.json
    //   ./node_modules/baz/index.js
    // 寻找上一级目录
    //   ../node_modules/baz.js
    //   ../node_modules/baz/package.json
    //   ../node_modules/baz/index.js
    // 再上一级目录
    ```
### import 命令加载 CommonJS 模块
- Node 采用 CommonJS 模块格式，模块的输出都定义在module.exports这个属性上面
- 在 Node 环境中，使用import命令加载 CommonJS 模块，Node 会自动将module.exports属性，当作模块的默认输出，即等同于export default
```
// a.js
module.exports = {
  foo: 'hello',
  bar: 'world'
};

// 等同于
export default {
  foo: 'hello',
  bar: 'world'
};
```
- 如果采用整体输入的写法（import * as xxx from someModule），default会取代module.exports，作为输入的接口
...

# 编程风格
1. 块级作用域
    - 不要再用var,用let完全取代
    - 在let和const之间，建议优先使用const
        - 尤其是在全局环境，不应该设置变量，只应设置常量
        - const优点：
            - 提醒不可改变
            - 符合函数式编程思想，运算不改变值，只是新建值;也有利于将来的分布式运算
            - 运行效率更好，Js 编译器对const进行优化，有利于提高程序的运行效率
            - 防止误修改
    - 所有的函数都应该设置为常量       

2. 字符串
    - 静态用单引号，不用双引号
    - 动态用模板字符串，不用+连接
3. 解构赋值
    - 对数组成员赋值，优先用
    - 函数参数为对象成员，优先用
    - 函数返回多个值，优先使用对象的解构赋值，而不是数组的解构赋值。
        - 这样便于以后添加返回值，以及更改返回值的顺序
4. 对象
    - 单行定义的对象，最后一个成员不以逗号结尾
    - 多行定义的对象，最后一个成员以逗号结尾
    - 对象尽量静态化，一旦定义，就不得随意添加新的属性
        - 定义对象时宜将需要的属性定义上
    - 如果非要添加属性，要使用Object.assign方法
    - 如果对象的属性名是动态的，可以在创造对象的时候，使用属性表达式定义
    - 对象的属性和方法，尽量采用简洁表达法

5. 数组
    - 使用扩展运算符（...）拷贝数组
    - 使用Array.from方法，将类似数组的对象转为数组

6. 函数
- 立即执行函数可以写成箭头函数的形式
        ```
        (() => {
          console.log('Welcome to the Internet.');
        })();
        ```
    - 需要使用函数表达式的场合，尽量用箭头函数代替。因为这样更简洁，而且绑定了this
    - 箭头函数取代Function.prototype.bind，不应再用self/_this/that绑定 this
    - 简单的、单行的、不会复用的函数，建议采用箭头函数。如果函数体较为复杂，行数较多，还是应该采用传统的函数写法
    - 所有配置项都应该集中在一个对象，放在最后一个参数
    - 不要在函数体内使用arguments变量，使用rest运算符（...）代替
        - rest运算符显式表明你想要获取参数
        - arguments是一个类似数组的对象，而rest运算符可以提供一个真正的数组
    - 使用默认值语法设置函数参数的默认值

7. Map结构
    - 注意区分Object和Map
        - 只有模拟现实世界的实体对象时，才使用Object。
        - 如果只是需要key: value的数据结构，使用Map结构。因为Map有内建的遍历机制

8. Class
    - 总是用Class，取代需要prototype的操作
        - Class的写法更简洁，更易于理解
    
    - 使用extends实现继承
        - 更简单，不会破坏instanceof运算

9. module
    - Module语法是JavaScript模块的标准写法，坚持使用这种写法
        - 使用import取代require
        - 使用export取代module.exports
    - 如果模块只有一个输出值，就使用export default，如果模块有多个输出值，就不使用export default，不要export default与普通的export同时使用
    - 不要在模块输入中使用通配符。因为这样可以确保你的模块之中，有一个默认输出（export default）
    - 如果模块默认输出一个函数，函数名的首字母应该小写
    - 如果模块默认输出一个对象，对象名的首字母应该大写




