# Vue

## 实例
```
var vm = new Vue({
       // this指向实例本身
       // 以下function内用 this.keyname 引用data/methods/computed...的值

    data:{
       ...
   }


    // 生命周期：
    created:function(){
   }

    // 计算属性
    computed:{
       xxx: function(){
       } 
        // or
        xxx:{
            get:function(){
           }
            set:function(){
           }
       }
   }

     watch:{
        // watch对象为要观测的将变化的变量
        xxx:function(){
            
       }
    }

    // method
    methods:{
         xxxx: function(){
       } 
   }
})
```
### 组件构造器
```
var MyComponent = Vue.extend({
})
// 所以 MyComponent 实例都将以预定义的扩展选项被创建
var myComponentInstance = new MyComponent()
```
所以得Vue组件都是被扩展的Vue实例

## 属性和方法
- 每个 Vue 实例都会代理其 data 对象里所有的属性
- 在实例创建之后添加新的属性到实例上，新属性是非响应的
- 自有属性和方法：带前缀 $，以便与代理的 data 属性区分
    - $data
    - $el
    - $watch

- **方法的 this 指向调用它的 Vue 实例**
> 不要在实例属性或者回调函数中（如vm.$watch('a', newVal => this.myMethod())）使用箭头函数。因为箭头函数绑定父上下文，所以 this 不会像预想的一样是 Vue 实例

### 实例生命周期
Lifecycle

## 模板
- 虚拟DOM
### 插值: {{ xxx }}
- v-once: 仅一次
- {{ 支持单个表达式计算 }}
    - 复杂表达式宜用计算属性
- v-html
    > 只对可信内容使用 HTML 插值，绝不要对用户提供的内容插值——可能导致XSS 攻击

### 指令
- 值支持单个表达式
- v-if
- v-bind:attr = ' variable '
    - 缩写 :attr
- v-on:event = ' function '
    - 缩写 @event
- 修饰符
    - .prevent
- 过滤器
    - 用于文本转换
    - 用在： 
        - {{ }} 插值 
        - v-bind
    - 在其他指令中实现更复杂的数据变换，可使用计算属性
    - 总接受表达式的值作为第一个参数
    - 可串联
    - 本质为函数，可接受参数
    `
   {{ message | filterA('arg1', arg2) }}
    `

## 计算属性：
- 复杂逻辑应当使用计算属性
- Computed vs Methods
    -  Methods可实现相同结果
    -  计算属性是基于它们的依赖进行缓存的，只有在它的相关依赖发生改变时才会重新求值
    -  只要发生重新渲染，method 调用总会执行该函数
    - 希望缓存用 computed，不希望缓存用 method
- Computed属性 vs Watch属性
    - watch：通用，但代码不简洁
    - 通常使用 computed 属性而不是命令式的 watch回调
    - 当要在数据变化响应时执行异步操作或开销较大的操作时可用watch(或vm.$watch)
- 默认只要getter，但也可显式定义setter
    - 用代码改变目标值时触发

## Class与style绑定
- 表达支持str / obj / arr类型
### 绑定Class
- 对象： 
    - 直接写法：v-bind:class='{ className : toBoolearn}'
    - 简介写法：v-bind:class='在data/cumputed里的objName'
   - 可与其他class并存
- 数组：
    - v-bind:class='[ className1, className2]'
    - className为obj的key
    - 内可用三元表达式
    - 可与数组结合  :class='[{ className1: toBoolean }, className2]'
- 在组件上使用class时，子组件原有的类不会被覆盖

### 绑定内联style
- 对象
    - 直接写法：v-bind:class='{ styleName : styleValue}'
    - 简介写法：v-bind:class='在data/cumputed里的objName'
- 数组
    - v-bind:class='[ styleObj1, styleObj2...]'
- 自动为需加特定前缀的css属性加前缀

## 条件渲染
- v-if
    - v-else
    - v-else-if
    - `<templatev-ifelseelse-if>`
    - key 
        - 添加唯一的key值，声明其为完全不同元素，可使其不会复用
- v-show
    - v-show 不支持 `<template>` 语法
- v-if vs v-show
    - v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销
    - 如需频繁地切换则用 v-show；如在运行时条件不太可能改变则用 v-if 


## 列表渲染
- 可遍历数组 / 对象 / 整数
    - v-for = "item in/of items"
    - v-for = "(item, index) in items"
    - v-for = "(item, [key], [index]) in object"
   - v-for = "n in 10"
   - `<templatev-for=item in items>`
- 使用了遍历的元素上，如需使用item值, 需用v-bind进行绑定：`<xxx v-for="item in items" :value=item.value>`
- 最近中使用：
   -`<my-compo≠ntv-for=item in items:key=item.id><my-compo≠nt>`
   - 当在组件中使用v-for时，key为必须
   - 组件上遍历的item和index不会自动传递到组件中，要用props
- v-for & v-if
    - 当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级,即v-if将分别在循环的每次迭代上运行
    - 如要使v-if优先级更高（如要按条件跳过循环的执行），可将v-if放在一个包装元素（或`<template>`）里面
- key: v-for的key要用v-bind:key来绑定到各个元素
- 数组更新检测
    - 变异方法——能触发视图更新
        - push / pop / shift / unshift / splice / sort / reverse
        - 直接检测
    - 非变异方法——不会改变原始数组，但总是返回一个新数组
        - filter / concat / slice 
        - 用新数组替换旧数组，可检测
    - 无法检测的数组变动
        - 利用索引设置值： vm.items[indexOfItem] = newValue
            - 解法:
                1. Vue.set(example1.items, indexOfItem, newValue)
                2. example1.items.splice(indexOfItem, 1, newValue)
        - 修改数组长度： vm.items.length = newLength
            - 解法：example1.items.splice(newLength)
- 使用过滤后的数组：
    1. 使用computed的数组
    2. 使用method计算后的数组，可传入参数：v-for="n in even(numbers)

## 事件监听
- 可绑定到method事件上
- 也可直接在用内联JS在行内定义，可传入参数
- 可在内联语句中访问原生dom事件，用$event将$event传入方法
- 事件修饰符
   - .stop 阻止事件冒泡
   - .prevent 阻止默认事件
   - .capture 使用事件捕获
   - .self 只当事件在该元素本身（而不是子元素）触发时触发回调
   - .once 只执行一次
    - 其它只能用于原生DOM 事件，once还可用于自定义的组件事件
    - 修饰符可以串联
- 按键修饰符 
    - @keyup.keynumber
    - 常用键值别名：
        - .enter / tab / delete(捕获 “删除” 和 “退格” 键) / esc / space / up / down / left / right
    - 可通过全局 config.keyCodes 对象自定义按键修饰符别名: Vue.config.keyCodes.f1 = 112
- 辅助符： .ctrl / .atl / .shift / .meta
    - 用于click事件： @keyup.alt.67 = "event" 
    - 用于键盘事件： @click.ctrl = "event"
- 鼠标符： .left / .right / .middle


## 表单绑定v-modal
- v-modal原理：语法糖，  
   <==> v-bind:value="something"  v-on:input="something = $event.target.value"
- 绑定类型：
    - input / textarea / radio / 单选select ==> 值
    - checkbox / 多选select ==> 数组

    > select的值与option未找到匹配项时， select将为未选择状态；在IOS中有bug，这将导致无法再选择第一项          ==> 宜提供一个disabled、value=""的option来规避

- 绑定动态value(改变v-modal绑定的值的值)
    - input[checkbox] ： `<input type="checkbox" v-model="toggle" :true-value="a" :false-value="b">` => vm.toggle === vm.a/b
    - input[radio]： `<input type="radio" v-model="pick" v-bind:value="a">` => 选中时：vm.pick === vm.a
    - select：`<select v-model="selected"><option v-bind:value="{ number: 123 }">123</option></select> `=>  选中时：vm.selected.number === 123

- 修饰符：
    - .lazy: 使input的绑定事件有input变为change
    - .number: 是输入值变为Number类型（如果转换结果为 NaN 则返回原值）
    - .trim

## 组件
- 未经slot传值的组件可用单标签：<compo≠ntName>
- 注册 
    - 全局注册：Vue.component(tagName, options)
       - 要在初始化根实例之前注册了组件才能在实例中起效
    - 局部注册：使用组件实例选项注册  
    - 这种封装也适用于其它可注册的 Vue 功能，如指令

```
	var Child = {
	  template: '<div>A custom component!</div>'
	}
	new Vue({
	  // ...
	  components: {
	    // <my-component> 将只在父模板可用
	    'my-component': Child
	  }
	})
```
- is属性：强制使某元素渲染为组件  
    ```
    <table>
      <tr is="my-row"></tr>
   </table>
    ```
- 组件的data必须为函数
    - 使每个组件的状态相互独立
- 组件传值：props down <==> event up
    - 常见错误：字面量语法 vs 动态语法
        - 向组件传值时，直接的写法只能传递字符串
        - 要传递非串值或变量，应用v-bind，才能使其值当作JS表达式计算
        
```
<!-- 传递了一个字符串"1" -->
<comp some-prop="1"></comp>
<!-- 传递实际的数字 -->
<comp v-bind:some-prop="1"></comp>
```
1. prop down
   - 组件实例的作用域是孤立的。这意味着不能在子组件的模板内直接引用父组件的数据，而是用 props 把数据传给子组件
   - HTML 特性是不区分大小写的，故当使用非字符串模版时，camelCased (驼峰式) 命名的 prop 需要转换为相对应的 kebab-case (短横线隔开式) 命名；如果你使用字符串模版，则无限制          
   - 单向数据流
      - 父属性变化时，子属性的prop也会跟着变
      - 不应在子组件内改变prop ==> 应将prop作为用data或computed的返回值
    - prop验证：将prop写为对象
 
```
	Vue.component('example', {
	  props: {
	    // 基础类型检测 （νll 意思是任何类型都可以）
	    propA: Number,
	    // 多种类型
	    propB: [String, Number],
	    // 必传且是字符串
	    propC: {
	      type: String,
	      required: true
	    },
	    // 数字，有默认值
	    propD: {
	      type: Number,
	      default: 100
	    },
	    // 数组／对象的默认值应当由一个工厂函数返回
	    propE: {
	      type: Object,
	      default: function () {
	        return { message: 'hello' }
	      }
	    },
	    // 自定义验证函数
	    propF: {
	      validator: function (value) {
	        return value > 10
	      }
	    }
	  }
	})
```       
2. event up
- 事件接口：
    - 使用 $on(eventName) 监听事件
    - 使用 $emit(eventName) 触发事件
- 父组件可在使用子组件的地方直接用 v-on 来监听子组件触发的事件
- 如要在子组件的根元素上监听一个原生事件而非自定义事件，可用@click.native
```
<my-component v-on:click.native="doTheThing"></my-component>
```
- 借用表单v-modal
    - 满足条件：
        1.  接受一个 value 属性 
        2. 在有新的 value 时触发 input 事件
```
    <custom-input
  :value="something"
  @input="value => { something = value }">
    </custom-input>
```  

    - 支持事件非input（如input[checkbox]）的组件不可直接使用，需自定义v-modal:  
   
```
    model: {
        prop: 'checked',
        event: 'change'
    },
      props: {
        // this allows using the value prop for a different purpose
        value: String
      },
```
> eg: 货币输入格式验证

- 非父子组件通信
    - 简单情形：可使用空白Vue实例作为中央事件总线
    - 复杂情形：使用专门的状态管理模式

- slot 分发
    - 单个（匿名）slot：
         - 字组件中没有slot接口时，父组件的包含内容将丢弃
        - 子组件只有一个为命名的slot时，父组件的包含内容全部插入替换slot所在DOM位置；
    - 具名slot
       - 子组件中写<slotname=slotName>, 父组件中包含在<子组件>中内容加上slot="slotName";
       - 仍可有一个匿名slot，作为默认slot, 给无匹配的内容片段使用 
    - 备用内容：最初在 <slot> 标签中的任何内容。备用内容在子组件的作用域内编译，并且只有在宿主元素为空，且没有要插入的内容时才显示备用内容
    - 作用域slot
```
<template scope="tempVariableName">
        ...
</template>
```
    - 固定使用`<template>`
    - 有特殊属性scope, 接收从子组件传来的prop
    - 传值路径：父 -> 子 -> 子slot -> 父中<子>包含内容
    - 常用于列表组件

- 动态组件
```
     <component is="xxx">
     </component>
     ... 
    data:{xxx:'组件名'}，
    component:{
        组件1，
        组件2
    }
```
    - 使用<keep-alive><keep-alive>包裹<compo≠nt>, 可将非活动组件保存在内存中，避免重新渲染

- 编写可复用接口
    - props: 允许外部环境传递数据给组件
    - events: 允许组件触发外部环境的副作用
    - slots: 允许外部环境将额外的内容组合在组件中

- js中访问子组件：ref
    - 使用 ref 为子组件指定一个索引 ID: ` <user-profile ref="profile"></user-profile>`
    - 访问：var child = vm.$refs.profile
    - 当 ref 和 v-for 一起使用时， ref 是一个数组或对象，包含相应的子组件
    - $refs 只在组件渲染完成后才填充，并且它是非响应式的。
    - 应避免在模版或计算属性中使用 $refs

- 异步组件
    -  Vue.js 允许将组件定义为一个工厂函数，动态地解析组件的定义

- 组件可在其模板内递归调用自身
    - 要确保递归有终止条件， 如调用时使用 v-if 并让他最终返回 false

- 组件间循环引用（A中有B,B中有A,互为子组件）
    - 全局注册的组件会自动处理
    - 使用模块化管理系统require/import组件时会报错
        - 解法：告诉模块化管理系统循环引用的组件间的处理优先级，在其中一个组件中告诉模块化管理系统，“A 虽然需要 B ，但是不需要优先导入 B”，在beforeCreate 生命周期钩子中去注册
```
beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue')
}
```
        
- 内联模板inline-template
    - 如果子组件有 inline-template 特性，组件将把它的内容当作它的模板，而不是把它当作分发内容，不常用

- X-Templates
    - 另一种定义模版的方式是在 JavaScript 标签里使用 text/x-template 类型，并且指定一个id, 不常用

- v-once
    - 当组件中包含大量静态内容时，可使用 v-once 将渲染结果缓存起来,以提高速度
```
    Vue.component('terms-of-service', {
	  template: '\
	    <div v-once>\
	      <h1>Terms of Service</h1>\
	      ... a lot of static content ...\
	    </div>\
	  '
	})
```


## 响应式原理
- 追踪变化：JavaScript 对象传给Vue实例的 data 选项 -> Vue 遍历所有属性，使用 Object.defineProperty 把这些属性全部转为 getter/setter ->getter/setter在内部让 Vue 追踪依赖，在属性被访问和修改时通知变化 - 每个组件实例都有相应的 watcher 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，致使它关联的组件得以更新

- 变化检测问题：受现代 JavaScript 的限制（以及废弃 Object.observe），Vue 不能检测到一般的对象属性的添加或删除，所以属性必须在 data 对象上存在才能让 Vue 转换它
    - Vue 不允许在已经创建的实例上动态添加新的data根级响应式属性。但可使用 Vue.set(vm.subobject, key, value) 方法将响应属性添加到次级嵌套及以下的对象上；
    - vm.$set （内部使用时为this.$set）为 Vue.set方法的别名

- 异步更新：数据更新时，DOM不会立刻更新，而是放到队列中，Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MutationObserver，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。
    - 如为了在数据变化之后等待 Vue 完成更新 DOM再执行某事件 ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。在组件内使用 vm.$nextTick()

## 过渡效果
- 单元素/组件的过渡
    `<transtion name="xxx">`
    - 适用情形：
       - 条件渲染 （使用 v-if）
         - 条件展示 （使用 v-show）
         - 动态组件
         - 组件根节点
    - 流程：
        - 当插入或删除包含在 transition 组件中的元素时做以下处理
            -  如有 CSS 过渡或动画，如果是，在恰当的时机添加/删除 CSS 类名
            - 如有JavaScript 钩子函数，这些钩子函数将在恰当的时机被调用
            - 如都没有，DOM 操作（插入/删除）在下一帧中立即执行
    - 类名：
        - xxx-enter
        - xxx-enter-active
        - xxx-leave
        - xxx-leave-active

    - 可添加transition过渡和animation动画
    - 自定义过渡类名
        - enter-class = "animated"
        - enter-active-class
        - leave-class
        - leave-active-class
        - 适用于与其他css动画库一起使用
    - 动画结束事件：transitionend / animationend

    - JS钩子
```
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"
  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
></transition>
...
methods: {
  // 进入中
    beforeEnter: function (el) {    // ...  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  enter: function (el, done) {
    done()
  },
  afterEnter: function (el) {
  },
  enterCancelled: function (el) {
  },
// 离开时
  beforeLeave: function (el) {
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  leave: function (el, done) {
    done()
  },
  afterLeave: function (el) {
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled: function (el) {
  }
}
```  

> 当只用 JavaScript 过渡的时候， 在 enter 和 leave 中，回调函数 done 是必须的 。 否则，它们会被同步调用，过渡会立即完成。  

> 推荐对于仅使用 JavaScript 过渡的元素添加 v-bind:css="false"，Vue 会跳过 CSS 的检测。这也可以避免过渡过程中 CSS 的影响。

- 初始渲染过渡
    - `<transition appear>`
    - 默认和进入和离开过渡一样，同样也可以自定义 CSS 类名

- 多组件条件切换的


.....

- 生命周期钩子
    - 常用类型：
         - Created：vue实例被生成后的一个生命周期钩子函数。(页面初始化数据加载一般写这里);
         - beforeCreate：给个loading界面 created撤销loading;
         - beforeDestory：你确认删除XX吗？
         - destoryed：当前组件已被删除，清空相关内容




