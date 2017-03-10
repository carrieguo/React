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
```这样一来，我们就能用类似local state 和 lifecycle hooks的附加功能。
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
当`Clock`第一次render到DOM中时，我们想创建一个定时器。在React中这称做"mounting"。
每当被`Clock`创建的 DOM 被移除时，我们想创建一个定时器。在React中这称做"unmounting"。
当一个组件mounts和unmounts时我们可以在声明特殊方法



