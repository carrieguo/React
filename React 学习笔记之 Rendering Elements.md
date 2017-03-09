#Rendering Elements

在 React app 中 elements元素是创建 blocks 的最小元素。

一个element元素描述了你想在屏幕上看到的：
```javascript
const element = <h1>Hello, world</h1>;
```

不同于浏览器中的 DOM 元素， React 元素是普通对象，易创建。React DOM 负责更新 DOM 使之与 React 元素相匹配。


##Rendering 元素  到 DOM 中去
