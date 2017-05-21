# Airbnb ES6 编码规范

1. 类型
2. 引用
    - 对所有的引用使用 const ；避免使用 var
        - 确保你无法对引用重新赋值
    -  如果你一定需要可变动的引用，使用 let 代替 var
3. 对象
    - 使用字面量创建对象而不用new Object()
    - 创建有动态属性名的对象时，可在字面量使用[属性名称]
        - 这样可以让你在一个地方定义所有的对象属性  
```
    const obj = {
      id: 5,S
      name: 'San Francisco',
      [getKey('enabled')]: true,
    };
```  

    - 使用对象方法的简写
    - 使用对象属性值的简写
    - 在对象属性声明前把简写的属性分组
    - 只对无效的属性名才包引号  

```
    const bad = {
        bar: 4,
        'data-blah': 5,
    }
```
    - 不要直接使用对象方法，比如hasOwnProperty, propertyIsEnumerable, isPrototypeof，而使用Object.prototype.xxxx.call(object,key)
```
    // bad
    console.log(object.hasOwnProperty(key));
    // good
    console.log(Object.prototype.hasOwnProperty.call(object, key));
```
    - 使用扩展运算符好于Object.assign来浅复制对象；使用扩展运算符来创建一个新对象并忽略或增加某些属性
```
    // very bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
    delete copy.a; // so does this
    
    // bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }
    
    // good
    const original = { a: 1, b: 2 };
    const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }
    
    const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```
 4. 数组
    - 使用字面量创建数组而不用new Array()
    - 使用扩展运算符来拷贝数组
```
    // bad
    const len = items.length;
    const itemsCopy = [];
    let i;
    
    for (i = 0; i < len; i += 1) {
      itemsCopy[i] = items[i];
    }
    
    // good
    const itemsCopy = [...items];
```
    - 使用Array.from 转换类数组对象为数组

5. 解构赋值
    - 可能的情况尽量使用
    - 函数有多返回值时应使用对象的解构，而非数组的解构！
        - 增加属性或者改变排序不会改变调用时的位置
```
    // bad
    function processInput(input) {
      // then a miracle occurs
      return [left, right, top, bottom];
    }
    
    // 调用时需要考虑回调数据的顺序。
    const [left, __, top] = processInput(input);
    
    // good
    function processInput(input) {
      // then a miracle occurs
      return { left, right, top, bottom };
    }
    
    // 调用时只选择需要的数据
    const { left, right } = processInput(input);
```
6. 字符串
    - 使用单引号''
    - 字符串长度超过100个字幕不应该手段换行或使用+连接
        - 打断字符串对于不利于使用和搜索
```
        // bad
        const errorMessage = 'This is a super long error that was thrown because \
        of Batman. When you stop to think about how Batman had anything to do \
        with this, you would get nowhere \
        fast.';
        
        // bad
        const errorMessage = 'This is a super long error that was thrown because ' +
          'of Batman. When you stop to think about how Batman had anything to do ' +
          'with this, you would get nowhere fast.';
        
        // good
        const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
```
    - 不用再串中使用eval()
    - 不对串做无效转译（\）
    
7. 函数
    - 使用命名函数表达式代替函数声明
        - 表达式的函数还应命名——匿名函数不利于在调用栈中定位错误
```
    // bad
    function foo() {
      // ...
    }
    // bad
    const foo = function () {
      // ...
    };
    // good
    const foo = function bar() {
      // ...
    };
```
    - 立即执行函数外面宜再包一个园括号
        - 如可用模块的时候就不用IIFE
```
        (function () {
          console.log('Welcome to the Internet. Please follow me.');
        }());
```
    - 不要再非函数的快级(if, while, etc)中声明函数，使用一个变量代替
    - 不要使用arguments, 使用rest运算符...代替
```
    // bad
    function concatenateAll() {
      const args = Array.prototype.slice.call(arguments);
      return args.join('');
    }
    
    // good
    function concatenateAll(...args) {
      return args.join('');
    }
```
    - 使用默认参数声明，而不要在函数内进行参数类型判断
    - 默认参数放在函数参数的最后面.
    - 不使用new Function()来创建函数
    - 不要在函数内对参数做任何改变
```
    // bad
    function f1(obj) {
      obj.key = 1;
    }
    
    // good
    function f2(obj) {
      const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
    }
```
8. 箭头函数
    - 用箭头函数替代函数声明，特别是在callback中
        - 当函数体比较复杂时，可考虑把逻辑部分转移到一个函数声明上
    - 如果函数体只有一个表达式适合用一行写出，那就把花括号、圆括号和 return 都省略掉。如果不是，那就不要省略
    - 如果函数体只有一个参数且函数体没有用花括号，可省略参数的原括号。否则，参数外应一直保留圆括号，这样更可读
    - 如果return一个表达式且有多行，宜外包一个圆括号使更易读.
    - 避免混淆了=> 和大于小于号（<=, >=, >, <）,把函数体包起来
   
9. 类和构造函数  
    - 总是使用 class。避免直接操作 prototype
        - class 语法更为简洁更易读
    - 使用 extends 继承
        - extends 是一个内建的原型继承方法并且不会破坏 instanceof
    - 方法可以返回 this 来帮助链式调用
```
        class Jedi {
          jump() {
            this.jumping = true;
            return this;
          }
        
          setHeight(height) {
            this.height = height;
            return this;
          }
        }
        
        const luke = new Jedi();
        
        luke.jump()
          .setHeight(20);
```
    -  可以写一个自定义的 toString() 方法，但要确保它能正常运行并且不会引起副作用。
```
        class Jedi {
          constructor(options = {}) {
            this.name = options.name || 'no name';
          }
        
          getName() {
            return this.name;
          }
        
          toString() {
            return `Jedi - ${this.getName()}`;
          }
        }
```
    - 类如无须定义constructor则可省略，不必写一个空的或只声明super()的constructor
10. 模块
    - 总是使用模组 (import/export) 而不是其他非标准模块系统（如require\module.exports）
    - 不使用通配符* 来import
        -  确保只有一个默认export
    - 不混合使用import和export
        - 一行虽简洁，但分开更清晰
    - 同一个路径的模块在同一行一次import
    - 不要export可变量
```
         // bad
          let foo = 3;
          export { foo };
        
          // good
          const foo = 3;
          export { foo };
```
    - 一个模块内只有一个export是，最好export default 而非命名export
    - 所有import写在最前面
        - import本身会提示
    - 不允许在import声明中捆绑webpack loader，写在webpack.config.js
11.  Iterators and Generators
    - 不要使用 iterators。使用高阶函数替代 for-of或for-in
        - 数组：使用map() / every() / filter() / find() / findIndex() / reduce() / some() / ... 
        - 对象：使用ject.keys() / Object.values() / Object.entries() 创建数组
    - 目前不要用generator，因为还没法很好地编译到 ES5

12. 属性
13. 变量
    - 一直使用 const 来声明变量，如果不这样做就会产生全局变量
    - 使用 const 声明每一个变量，不要多变量连用
        - 增加新变量将变的更加容易，而且你永远不用再担心调换错 ; 跟 ,
    - 先写const组再写let组
    - 在你需要的地方给变量赋值，放在一个合理的位置。不需要放在快级最前面！
    - 不要链式赋值
        - 链式赋值默认创建全局变量！
```
        // bad
        (function example() {
          // JavaScript interprets this as
          // let a = ( b = ( c = 1 ) );
          // The let keyword only applies to variable a; variables b and c become
          // global variables.
          let a = b = c = 1;
        }());
        console.log(a); // undefined
        console.log(b); // 1
        console.log(c); // 1
        
        // good
        (function example() {
          let a = 1;
          let b = a;
          let c = a;
        }());
        console.log(a); // undefined
        console.log(b); // undefined
        console.log(c); // undefined
        // the same applies for `const
```
    - 避免使用++/--？
        - num += 1更形象，也可避免前后一元的差异
14. 变量提升
    - 不用var
    - 匿名函数表达式的变量名会被提升，但函数内容并不会!
```
    function example() {
      console.log(anonymous); // => undefined
    
      anonymous(); // => TypeError anonymous is not a function
    
      var anonymous = function() {
        console.log('anonymous function expression');
      };
    }
```
    - 命名的函数表达式的变量名会被提升，但函数名和函数函数内容并不会,即使函数名与变量名相同
    - 函数声明的名称和函数体都会被提升

15. 比较运算符和等号
    - 优先使用全等
    - 条件表达式例如 if 语句通过抽象方法 ToBoolean 强制计算它们的表达式
        - 对象=>T, 即使{}/[] => T !
    - 对布尔值用简写，但对串和数组用比较
```
    // bad
    if (isValid === true) {
      // ...
    }
    // good
    if (isValid) {
      // ...
    }
    // bad
    if (name) {
      // ...
    }
    // good
    if (name !== '') {
      // ...
    }
    // bad
    if (collection.length) {
      // ...
    }
    // good
    if (collection.length > 0) {
      // ...
    }
```
    - 当switch语句的case和default分支中含有声明（let/const/function/cladd）时, 应用大括号包起来
        - 但多个分支定义相同变量时会出错
```
        // bad
        switch (foo) {
          case 1:
            let x = 1;
            break;
          case 2:
            const y = 2;
            break;
          case 3:
            function f() {
              // ...
            }
            break;
          default:
            class C {}
        }
        
        // good
        switch (foo) {
          case 1: {
            let x = 1;
            break;
          }
          case 2: {
            const y = 2;
            break;
          }
          case 3: {
            function f() {
              // ...
            }
            break;
          }
          case 4:
            bar();
            break;
          default: {
            class C {}
          }
        }
```
        - 三元符最好不要链式连接而且最好写在一行
        - 使用|| ！ 替代非必要的三元符
16. 代码块
    - 用括号括起来，尽管对于单语句不必要
17. 注释
    - 在评论对象上面另起一行使用单行注释。在注释前插入空行    
    - 给注释增加 FIXME 或 TODO 的前缀可以帮助其他开发者快速了解这是一个需要复查的问题，或是给需要实现的功能提供一个解决方式。
        - 使用 // FIXME: 标注问题
        - 使用 // TODO: 标注问题的解决方式
18. 空白
    - 使用 2 个空格作为缩进
    - 在花括号前放一个空格
    - 在控制语句（if、while 等）的小括号前放一个空格
    - 在函数调用及声明中，不在函数的参数列表前加空格：aa () X
    - 使用空格把运算符隔开
    - 在文件末尾插入一个空行
    - 在使用长方法链时进行缩进。使用点 .放行前 强调这是方法调用而不是新语句    
    
```
        // good
        $('#items')
          .find('.selected')
            .highlight()
            .end()
          .find('.open')
            .updateCount();
```     
    
    - 在块末和新语句前插入空行
    - 快内不要加空行
    - 圆括号/方括号内不要加空白，花括号内加空白
    - 避免一行代码长超过100字符（含空白） 


```
        // bad
        if ( foo ) {
          console.log(foo);
        }

        const foo = [ 1, 2, 3 ];
        console.log(foo[ 0 ]);

        const foo = { clark: 'kent' };
```  

19. 逗号
    - 不前置
    - 对象、数组、函数参数末元素也加上
        - 使git diffs更干净
        - babel等转译器会移除，不必担心
        - 末元素为rest(...)时不能加！
20. 分号
21. 类型转换
    - 转串：最佳方法：string（xxx）\
        - 不好方法：'' + xxx => 调用xxx的valueOf()方法
        - 不好方法：xxx.toString() => 不保证成功
    - 转数字：使用parseInt
        - 如果因为某些原因 parseInt 成为你所做的事的瓶颈而需要使用位操作解决性能问题时，留个注释说清楚原因和你的目的
    - 小心使用位操作运算符。数字会被当成 64 位值，但是位操作运算符总是返回 32 位的整数，最大的 32 位整数是 2,147,483,647，大于此数操作会出错
    - 转布尔：！！或Boolean(xxx)

22. 命名规则
    - 避免单字母命名。命名应具备描述性
    - 使用驼峰式命名对象、函数和实例
    - 使用帕斯卡式命名构造函数或类(首字母大写)
    - 别保存 this 的引用。使用箭头函数或 Function#bind
    - 如果模块文件只输出一个类，那你的文件名必须和类名保持一致
    - 默认export的函数时使用驼峰式命名
    - export构造函数、类、单例、函数库、空对象时使用帕斯卡式命名
    - 缩写和缩略词应全部大写或全部小写：const HTTPRequest

23. 存储器
    - 存取函数使用getVal()/setVal(),不使用getter/setter
    - 属性为布尔使用isVal()/hasVal()

24. 事件
    - 当给时间附加数据时（无论是 DOM 事件还是私有事件），传入一个哈希而不是原始值     
```
    // bad
    $(this).trigger('listingUpdated', listing.id);
    $(this).on('listingUpdated', function(e, listingId) {
      // do something with listingId
    });
    // good
    $(this).trigger('listingUpdated', { listingId : listing.id });
    $(this).on('listingUpdated', function(e, data) {
      // do something with data.listingId
    });
```
25. jQuery
    - 使用 `$` 作为存储 jQuery 对象的变量名前缀 : $sidebar
    - 对JQ对象应先使用变量存存储后再使用
    -  对 DOM 查询使用层叠 $('.sidebar ul') 或 父元素 > 子元素及find 
28. 测试
        -         