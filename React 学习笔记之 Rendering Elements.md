#Rendering Elements

在 React app 中 elements元素是创建 blocks 的最小元素。

一个element元素描述了你想在屏幕上看到的：
```javascript
const element = <h1>Hello, world</h1>;
```

不同于浏览器中的 DOM 元素， React 元素是普通对象，易创建。React DOM 负责更新 DOM 使之与 React 元素相匹配。


##Rendering 元素  到 DOM 中去
```javascript
<div id="root"></div>

const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```
##更新 Render 后的元素
React 元素是不可变的。一旦你创建一个元素，你就不能改变它的子元素或属性。一个元素就像电影里的一个单帧：它代表了在某个时间点上的UI`不是很懂==`。

目前凭我们掌握的知识，更新UI的唯一方法就是创建一个新元素，并将其传递给 `ReactDOM.render()`.

参考下面这个滴答时钟的例子

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
回调函数`setInterval()`每秒调用一次`ReactDOM.render()`

##React 只在必要时才更新
React Dom 比较之前的元素及其子元素，只把需要更新的DOM更新到所需的状态。
