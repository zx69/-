# Airbnb React 编码规范

- 基本规则
    - 每个文件只写一个模块.
        - 但是多个无状态模块可以放在单个文件中.、
    - 推荐使用JSX语法.
    - 不要使用 React.createElement，除非从一个非JSX的文件中初始化你的app.

- 创建模块Class vs React.createClass vs stateless
    - 如果你的模块有内部状态或者是refs, 推荐使用 class extends React.Component 而不是 React.createClass
```
// bad
 const Listing = React.createClass({
    // ...
    render() {
      return <div>{this.state.hello}</div>;
    }
});

// good
  class Listing extends React.Component {
    // ...
    render() {
      return <div>{this.state.hello}</div>;
    }
}
```
    - 如果你的模块没有状态或是没有引用refs， 推荐使用普通函数（非箭头函数）而不是类
```
     // bad
  class Listing extends React.Component {
    render() {
      return <div>{this.props.hello}</div>;
    }
  }
  // bad (relying on function name inference is discouraged)
  const Listing = ({ hello }) => (
    <div>{hello}</div>
  );
  // good
  function Listing({ hello }) {
    return <div>{hello}</div>;
  }
```
- 命名：
    - 扩展名: 使用 .jsx 扩展名
    - 文件名：使用帕斯卡命名. 如, ReservationCard.jsx
    - 引用命名: React模块名使用帕斯卡命名，实例使用骆驼式命名 
```
  // bad
  import reservationCard from './ReservationCard';

  // good
  import ReservationCard from './ReservationCard';

  // bad
  const ReservationItem = <ReservationCard />;

  // good
  const reservationItem = <ReservationCard />;
```
    - 模块命名: 模块使用当前文件名一样的名称. 比如 `ReservationCard.jsx` 应该包含名为 `ReservationCard`的模块. 但是，如果整个文件夹是一个模块，使用 `index.js`作为入口文件，则直接使用 `index.js`作为文件名而 文件夹名作为模块名:  

```jsx
  // bad
  import Footer from './Footer/Footer';
  // bad
  import Footer from './Footer/index';
  // good
  import Footer from './Footer';
```
    - 高阶模块命名
    - 属性命名
- 声明模块
- 代码对齐
```
  // bad
  <Foo superLongParam="bar"
       anotherSuperLongParam="baz" />

  // good, 有多行属性的话, 新建一行关闭标签
  <Foo
    superLongParam="bar"
    anotherSuperLongParam="baz"
  />

  // 若能在一行中显示, 直接写成一行
  <Foo bar="bar" />
    
  // 子元素按照常规方式缩进
  <Foo
    superLongParam="bar"
    anotherSuperLongParam="baz"
  >
    <Quux />
  </Foo>
```
- 引号
    - 对于JSX属性值总是使用双引号("), 其他均使用单引号(').
- 空白
    - 总是在自动关闭的标签前加一个空格，正常情况下也不需要换行
    - 不要在JSX {} 引用括号里两边加空格
- 属性
    - JSX属性名使用骆驼式风格
    - 如果属性值为 true, 可以直接省略
```
  // bad
  <Foo
    hidden={true}
  />
  // good
  <Foo
    hidden
  />
```
    - <img> 标签总是添加 alt 属性
    - 避免使用数组的index来作为属性key的值，推荐使用唯一ID
```
  - // bad
  {todos.map((todo, index) =>
    <Todo
      {...todo}
      key={index}
    />
  )}

  // good
  {todos.map(todo => (
    <Todo
      {...todo}
      key={todo.id}
    />
  ))}
```
    - 对于所有非必须(non-required)的属性，总是手动去定义defaultProps属性
```
  // good
  function SFC({ foo, bar }) {
    return <div>{foo}{bar}</div>;
  }
  SFC.propTypes = {
    foo: PropTypes.number.isRequired,
    bar: PropTypes.string,
    children: PropTypes.node,
  };
  SFC.defaultProps = {
    bar: '',
    children: null,
  };
```
- Refs
    - 总是在Refs里使用回调函数
```
  // bad
  <Foo
    ref="myRef"
  />

  // good
  <Foo
    ref={(ref) => { this.myRef = ref; }}
  />
```
- 括号
    - 将多行的JSX标签写在 ()里

- 标签
    - 对于没有子元素的标签来说总是自己关闭标签
    - 如果模块有多行的属性， 关闭标签时新建一行
- 函数
    - 使用箭头函数
    - 当在 render() 里使用事件处理方法时，提前在构造函数里把 this 绑定上去.
        -  在每次 render 过程中， 再调用 bind 都会新建一个新的函数，浪费资源
```
 // bad
  class extends React.Component {
    onClickDiv() {
      // do stuff
    }

    render() {
      return <div onClick={this.onClickDiv.bind(this)} />
    }
  }

  // good
  class extends React.Component {
    constructor(props) {
      super(props);

      this.onClickDiv = this.onClickDiv.bind(this);
    }

    onClickDiv() {
      // do stuff
    }

    render() {
      return <div onClick={this.onClickDiv} />
    }
  }
```
    - 不要给所谓的私有函数添加 _ 前缀，本质上它并不是私有的  
    - 在 render 方法中确保用 return 返回一个值
- 生命周期命令
    



