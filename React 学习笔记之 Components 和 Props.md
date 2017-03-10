#Components and Props

Components 能将UI分割成独立的、可重用的组件，每个组件都能独立的起作用。
从概念上将，组件就类似于 JavaScript 函数。它们接受任意的输入（称作"props"）并返回出现在屏幕上的 React 元素描述。

##Functional and Class Components 功能和类组件
定义一个component 最简单的方式就是写一个 JavaScript 函数：
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
这个函数是一个有效的React 组件，它接收了一个“props”对象作为参数，并返回一个React element. 这种字面上的JavaScript函数组件，我们称这样的组件为“功能性组件“
你也可以用ES6 class 来定义组件：
```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
以上的两个组件功能一致。
##Rendering 一个组件
之前，我们遇到过这种情况：React 元素代表 DOM tag:

```javascript
const element = <div />;
```
其实，元素也可以代表 用户自定义的组件
```javascript
const element = <Welcome name="Sara" />;
```

当React看到一个元素代表的是一个用户自定义的组件，它会将JSX属性作为一个单独的对象传递给这个组件。
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```
``
注意：
* 组件名首字母要大写
* `<div />`代表DOM tag ，代表组件的`<Welcome />`标签也要闭合
* components 只能返回一个根元素
``
##分解Components
不要害怕把components分解成小段的components。
例如，看下面的Components:
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

接收了`author`(一个对象)，`text`(一个字符串)和`date`(一个date)作为props，描述了在社交媒体上的评论。
由于多层的嵌套，该组件很难改变，也很难重用其中的单个部分。下面我们来提取几个组件。
先来提取`Avatar`:
```javascript
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```
`Avatar`不需要知道它在`Comment`中被Rendered.这也是为什么我们给它的props命名，为`user`代替了`author`,更宽泛一些。


我们建议从组件自己的观点来命名props，而不是从上下文。
再来提取`UserInfo`component
```javascript
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```
这样我们的`Comment`就更简单明了了
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

提取组件看起来很麻烦，但对大型App而言，带来了可重用的组件。
建议如果在你的UI中，存在重复使用的部分（`Button`,`Panel`,`Avatar`），或是很复杂的部分（`App`, `FeedStory`, `Comment`），分解组件是明智的。

##Props 只读
不管你声明一个组件作为一个函数或是一个类,绝对不能修改自己的props。看下面的`sum`函数：
```javascript
function sum(a, b) {
  return a + b;
}
```
上面的函数是”pure“的，因为没有要改变传入值，始终根据传入值返回相同的计算结果。
相对应，下面的函数”impure“：
```javascript
function withdraw(account, amount) {
  account.total -= amount;
}
```
React非常灵活，但有一个严格的规则：
所有的React组件必须像”pure“函数那样，不改变自己的props。当然，程序UI随时间动态改变。
之后我们会介绍State,State允许React组件随用户行为、网络相应等等动态得改变输出结果，且不违反这条规则。
