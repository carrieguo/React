#状态和生存期
之前我们写了一个滴答时钟的例子，通过调用`ReactDOM.render()`来改变render后的结果。
```javascript
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```
下面我们来正式完善`clock`组件，使其封装，可复用。创建自己的计时器并且每秒自己更新。
```javascript
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

##把Function转换成class
把`clock`从功能性组件转换为类组件分五部
1. 新建一个同名称的 [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)，继承自 `React.Component`。 
2. 增加一个`render()`方法。
3. 把函数体挪到 `render()`方法里。
4. 把`render()`方法中的`props`替换为`this.prop`。
5. 删除其余的空函数声明
```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
这样一来，我们就能用类似local state 和 lifecycle hooks的附加功能。
##给 Class 添加 Local State
分以下三步从 props 挪 `date` 到 state:

1) 在`render()`方法中，用`this.state.date`替换`this.props.date`:
```javascript
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```
2) 新增一个 [class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor) 赋值给初始`this.state`:
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props); //传递 props 到构造基本构造函数
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
3）从`<Clock />`元素中移除`date`prop
```javascript
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
##Adding Lifecycle Methods to a Class
假如程序中有很多组件，我们需要在组件销毁后及时释放资源。

当`Clock`第一次 render 到 DOM 中时，我们想创建一个定时器。在React中这称做"mounting"。

每当被`Clock`创建的 DOM 被移除时，我们想清除这个定时器。在React中这称做"unmounting"。

当一个组件mounts和unmounts时，我们可以在这个组件类上声明特殊方法执行一些代码：
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
这些方法被叫做“lifecycle hooks”。
component 输出被 render 到 DOM 后， `componentDidMount()` 钩子就会运行。在这里设置一个定时器比较好。
```javascript
 componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```
我们在`componentWillUnmount()`lifecycle hook中清除定时器：
```javascript
componentWillUnmount() {
    clearInterval(this.timerID);
  }
```
最后，我们来实现`tick()`方法。
`this.setState()`用来更新组件本地状态
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
让我们快速回顾一下发生了什么以及方法被调用的顺序:
1) `<Clock />`作为参数被传递给 `ReactDOM.render()`, React调用`Clock`组件的构造函数。当`Clock`需要显示当前时间时，用一个包含当前时间的对象来初始化`this.state`。我们接下来更新state。
2) 之后React调用`Clock`组件的`render()`方法。据此，React知道显示到屏幕上什么内容。之后，React更新DOM来匹配`Clock`的render输出。
3) `Clock`输出嵌入到 DOM 之后，React 调用 `componentDidMount()`生命周期钩子。其中，`Clock`组件让浏览器穿件一个定时器，每秒调用一次`tick()`。
4) 浏览器每秒调用一次`tick()`方法。其中，`Clock`组件通过调用`setState()`来安排一次UI更新，`setState()`带有一个包含当前时间的对象。多亏了`setState()`调用，React 知道状态被改变了，就会再一次调用`render()`方法来得知显示到屏幕上什么内容。这一次，`this.state.date`的值在render()方法中会改变，这样一来，render输出将会包含当前时间。React相应的更新DOM。
5) 如果 `Clock` 组件一旦从DOM 中移除，React 将调用 `componentWillUnmount()`生命周期钩子，计时器也被销毁。
##正确使用State

使用`setState()`时要注意以下三点。
###不要直接修改State

例如，下面的代码不会re-render一个组件
```javascript
// Wrong
this.state.comment = 'Hello';
```
我们要用`setState()`方法
```javascript
// Correct
this.setState({comment: 'Hello'});
```
你只能在构造函数中初始化`this.state`。
###State更新可能是异步的
为了提升性能，React 可能批处理多个 `setState()`时分成单个的更新操作。
由于`this.props`和`this.state`可能会异步更新，你应不能根据他们的值来得出接下来的state。
例如，下面的代码可能更新counter失败
```javascript
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
为了修复这个错误,我们用`setState()`的第二种方式，接收一个函数而不是一个对象。函数将接收先前的state作为第一个参数，此时props更新被作为第二个参数：
```javascript
// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```
上面是箭头函数写法，下面是常规写法
```javascript
// Correct
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});
```
###State Updates are Merged

When you call setState(), React merges the object you provide into the current state.

For example, your state may contain several independent variables:
```javascript
constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```
Then you can update them independently with separate setState() calls:


```javascript
componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```
The merging is shallow, so this.setState({comments}) leaves this.state.posts intact, but completely replaces this.state.comments.
##The Data Flows Down










