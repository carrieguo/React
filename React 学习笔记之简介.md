##简介

React 是一个灵活的JS框架。你可以用它创建新的应用程序,也可以将它逐步引入到一个现有的代码库。

##JSX 简介

```javascript
const element = <h1>Hello, world!</h1>;
```

以上的变量声明，既不是HTML语法，也不是 string。

这是JSX，它是JavaScript的语法扩展。我们建议在React中使用JSX来更好的体现UI。JSX可能让你想到 templare language,但 JSX 包含 JavaScript 的所有功能。

JSX使得React "元素化"。我们可以把它们 rendering 成 DOM 元素。
###在JSX中嵌入表达式

在JSX中可以通过大括号`{}`嵌入任意的[JavaScript表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions)。
例如，`2 + 2`, `user.firstName`, `formatName(user)` 都是合法表达式：

```javascript
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```
为了提升可读性，我们把JSX拆分成多行。这样一来，我们最好用括号包括代码段，以防JavaScript中的“自动分号插入”机制[`ASI`](http://stackoverflow.com/questions/2846283/what-are-the-rules-for-javascripts-automatic-semicolon-insertion-asi)。
###JSX也是一个表达式

编译后，JSX表达式会成为常规的JavaScript对象。

这意味着你可以把JSX代码段放到if语句和for循环中，将其分配给变量，作为参数接收，从函数中返回：
```javascript
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

`注意：

*标签要闭合，类似于XML

*相比较于HTML，JSX更接近JavaScript,React DOM采用 camelCase(驼峰命名法)，不采用HTML命名法。`
###JSX防注入攻击

将用户输入嵌到 JSX 代码中更安全。
```javascript
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```

默认情况下，在rendering之前，React DOM会[escapes](http://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html) `是转义的意思吗？`JSX中的所有值。因此，这个机制确保了你的程序之外的东西不会被注入。在rendering之前，所有的代码都被转换为一个字符串。这有利于防止XSS（跨站脚本）攻击。

##JSX Represents Objects
Babel 编译JSX时调用了`React.createElement()`方法。

下面的两个例子达到了相同的结果：
```javascript
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```
```javascript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```
`React.createElement()` 可以执行一些检查,帮助你写出没有错误的代码，但本质上它创建了这样的一个对象:
```javascript
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
};
```
这些对象被称为“React elements”。你可以把它们作为描述你想在屏幕上看到的东西。React读取这些对象,并用它们构造DOM,并更新DOM tree。

`Tip:
我们建议寻找带有“Babel”语法方案的编辑器,这样两个ES6和JSX代码正确高亮显示。`
