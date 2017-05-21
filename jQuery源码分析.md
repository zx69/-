## jQuery源码分析

```
(function(window,undefined){
    // 以window为参原因：1.内部引用只找到参数而不用找全局，速度快；2. 压缩时可简化window名;
	// 以undefined为参原因：undefined在某些浏览器（：ie7）下非关键字，可被赋值；为参使内部的使用不会被外面的赋值干扰；
    // jQuery中的方法：
    // 1.实例方法：$().xx()    其中的$( )实为一jQuery对象
    // 2.静态方法：$.xx()   更底层，原生JS也可用，使用$.extend生成

    // jQ中复杂选择器的实现：sizzle ——可单独下载

    （21,94）定义变量和函数jQuery=function(){};

    rootjQuery:  jQ根目录，==$(document)；
	// 定义为变量的意义：1.使可压缩；2.使有意义，方便维护；

    class2type={'[Object String]':'string',[Object Array]: 'array',.....}
	// 用于$.type方法

    jQuery面向对象写法：
	function $(){
	    return new $.prototype.init();
	}
    $.prototype.init=function(){};
    $.prototype.css=function(){};
    $.prototype.init.prototype= $.prototype;
    //使用——
    $().css();
// =>$( ).xx( ) 实例方法中的$( )实为一对象,构造函数为$.prototype.init();



    (96,283) 给JQ对象，添加一些方法和属性
    $.prototype.init=function(selector,context,rootjQuery)
    //=>$('li','ul') :在ul中查找li
    //=>$('<li>',{title:'hi',html:'abcd'}) : 创建数组自带属性（其中方法名写为jq形式，即html而非innerHTML）
    //如果没有指定或给定null 或 undefined，context是当前的document

	//$()自带遍历的实现：
    /*$('li').css('color','red');
    1.$('li'):document.getElementsByTagName('li');
    2.this={
        0:'li',
        1:'li',
        2:'li',
        length:3
    }
    3.for(var i=0;i<this.length;i++){
        this[i].style.color='red';
    }*/  

    //=>故可用写法：$('li')[1].style.color='red';

    $.parseHTML(data,context,keepScript)
    //将字符串解析到一个DOM节点的数组中

    $.merge(fArr,sArr);
    //合并数组

    $.makeArray(selector);
    //转换一个类似数组的对象成为真正的JavaScript数组

    $(function(){})===$(document).ready(function(){});
    //区别window.onload:DOM加载完即执行，而不会等全部内容加载完后再执行；

    selector.toArray();
    //返回一个包含jQuery对象集合中的所有DOM元素的数组

    selector.get(x)
    //无参同toArray,有参同toArray[x]

    栈（先进后出）<------> 队列（先进先出）
    selector.end()到
    //链式写法中返回前一栈，可用end的方法都调用了pushStack


    (285,347) extend: JQ的继承方法

    extend扩展两种：
    a.只写一个对象字面量时:
    1 扩展工具方法：$.extend({
        aaa:function(){},
        bbb:function(){}
    })
    =>$.aaa();
    2 扩展JQ实例方法：$.extend({
        aaa:function(){},
        bbb:function(){}
    })
    =>$().aaa();

    b.写多个对象字面量时，后面的对象都扩展到第一个对象上；
    var a={};
    $.extend(a,{name:'hello'},{age:30});

    extend做拷贝：
    1.浅拷贝: $.extend(a,b);
    2.深拷贝: $.extend(true,a,b);


    (349,817)$.extend()扩展工具方法：
    $.noConflict();

    //$.ready();
    //返回延迟对象的resolve() =>实际用resolveWith(this指向,argument),同resolve当可传参；
    $.holdReady(boolean);
    //指示是否暂停或恢复被请求的ready事件,常用来在ready事件发生之前动态加载其它的Script

    $.isFunction()
    $.isArray() //=>es5已支持isArray,可不用$;
    $.isNumeric() //=>vs原生typeof:排除NaN的干扰；
    $.type()













}
```