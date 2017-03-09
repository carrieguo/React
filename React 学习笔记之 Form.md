##约束性和非约束性组件

约束性组件，由React管理value，而非约束性组件的value由原生的DOM管理。 
他们的写法上也有很大区别。

非约束性组件这么写：
```javascript
<input type="text" defaultValue="a" />
```
这个 defaultValue 其实就是原生DOM中的 value 属性。这样写出的来的组件，其value值就是用户输入的内容，React完全不管理输入的过程。

而约束性组件是这么写的：
```javascript
<input type="text" value={this.state.name} onChange={this.handleChange} />
```
//...省略部分代码
```javascript
handleChange: function(e) {
  this.setState({name: e.target.value});
}
```
这里，value属性不再是一个写死的值，他是 this.state.name，而 this.state.name 是由 this.handleChange 负责管理的。 
这个时候实际上 input 的 value 根本不是用户输入的内容。而是onChange 事件触发之后，由于 this.setState 导致了一次重新渲染。不过React会优化这个渲染过程，实际它依然是通过设置input的value来实现的。

但是一定要注意，约束性组件显示的值和用户输入的值虽然很多时候是相同的，但他们根本是两码事。约束性组件显示的是 this.state.name 的值。你可以在handleChange中对用户输入的值做任意的处理，比如你可以做错误校验。

对比约束性组件和非约束性组件的输入流程：

* 非约束性组件： `用户输入A -> input 中显示A`
* 约束性组件： `用户输入A -> 触发onChange事件 -> handleChange 中设置 state.name = “A” -> 渲染input使他的value变成A`

约束性组件，它能更好的控制组件的生命流程。

##更统一和更规范的接口

React 把 input,textarea 和 select 三个组件做了抽象和封装，他们的用法变得非常统一，你基本上可以当做同一个组件来用。

他们现在有统一的 value 属性 和 onChange 事件，现在对于这三种组件你都可以这样写
```javascript
<input type='text' name='intro' id='intro' value={this.state.email} onChange={this.handleEmail} />
<textarea type='text' name='intro' id='intro' value={this.state.intro} onChange={this.handleIntro} />
<textarea type='text' name='intro' id='intro' value={this.state.intro} onChange={this.handleIntro} />
```
不过 chekbox有和上面三个不一样，因为checkbox改变的不是 value ，而是 checked 状态。 
你可以这样写：
```javascript
<input type='radio' name='gender' checked={this.state.male} onChange={this.handleGender} value='MALE' />
<input type='radio' name='gender' checked={!this.state.male} onChange={this.handleGender} value='FEMALE' />
```
##一个示例

下面是一个包含了 input,textarea, select, radio 的表单，并且做了简单的校验：
```javascript
  var MyForm = React.createClass({
    getInitialState: function() {
      return {
        email: "",
        intro: "",
        city: "hz",
        male: true, //性别
        emailError: "",
        introError: ""
      };
    },
    handleEmail: function(e) {
      var value = e.target.value;
      var error = '';
      if(!(/^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/.test(value))) {
        error = '请输入正确的Email';
      }
      this.setState({
        email: value,
        emailError: error
      });
    },
    handleIntro: function(e) {
      var value = e.target.value;
      var error = "";
      if(value.length < 10) {
        error = "介绍不能少于十个字";
      }
      this.setState({
        intro: value,
        introError: error
      });
    },
    handleCity: function(e) {
      var value = e.target.value;
      this.setState({
        city: value,
      });
    },
    handleGender: function(e) {
      var male = !!(e.target.value == 'MALE');
      this.setState({
        male: male
      });
    },
    render: function() {
      return (
        <div>
        <p>
          <label htmlFor='email'>email:</label>
          <input type='text' name='intro' id='intro' value={this.state.email} onChange={this.handleEmail} />
          <span>{this.state.emailError}</span>
        </p>
        <p>
          <label htmlFor='intro'>intro:</label>
          <textarea type='text' name='intro' id='intro' value={this.state.intro} onChange={this.handleIntro} />
          <span>{this.state.introError}</span>
        </p>
        <p>
          <label htmlFor='city'>所在城市:</label>
          <select  name='city' id='city' value={this.state.city} onChange={this.handleCity}>
            <option value='hz'>杭州</option>
            <option value='bj'>北京</option>
            <option value='sh'>上海</option>
          </select>
        </p>
        <p>
          <label>性别:</label>
          <input type='radio' name='gender' checked={this.state.male} onChange={this.handleGender} value='MALE' />
          <input type='radio' name='gender' checked={!this.state.male} onChange={this.handleGender} value='FEMALE' />
        </p>
        </div>
        )
    }
  });

  React.render(
    <MyForm />,
    document.getElementById("div1")
    );
    ```
##非约束性组件使用ref获取输入值

组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 DOM diff ，它可以极大提高网页的性能表现。

但是，有时需要从组件获取真实 DOM 的节点，这时就要用到 ref 属性。

需要注意的是，由于 this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。

```javascript
<div id="div1"></div>
<script type="text/babel">
    var MyForm = React.createClass({
        getInitialState: function () {
            return {
                email: "",
                intro: "",
                city: "hz",
                male: true, //性别
                emailError: "",
                introError: ""
            };
        },
        handleGender: function (e) {
            var male = !!(e.target.value == 'MALE');
            this.setState({
                male: male
            });
        },
        handleSubmit: function (e) {
            e.preventDefault();
            console.log(this.refs['myEmail'].value);
            console.log(this.refs['myIntro'].value);
            console.log(this.refs['city'].value);
        },
        render: function () {
            return (
                    <form onSubmit={this.handleSubmit}>
                        <p>
                            <label htmlFor='email'>email:</label>
                            <input type='text' name='email' id='email' ref="myEmail" defaultValue={this.state.email}/>
                            <span>{this.state.emailError}</span>
                        </p>
                        <p>
                            <label htmlFor='intro'>intro:</label>
                            <textarea type='text' name='intro' id='intro' ref="myIntro" defaultValue={this.state.intro}/>
                            <span>{this.state.introError}</span>
                        </p>
                        <p>
                            <label htmlFor='city'>所在城市:</label>
                            <select name='city' id='city' ref="city" defaultValue={this.state.city}>
                                <option value='hz'>杭州</option>
                                <option value='bj'>北京</option>
                                <option value='sh'>上海</option>
                            </select>
                        </p>
                        <p>
                            <label>性别:</label>
                            <input type='radio' name='gender' checked={this.state.male} onChange={this.handleGender}
                                   value='MALE'/>
                            <input type='radio' name='gender' checked={!this.state.male} onChange={this.handleGender}
                                   value='FEMALE'/>
                        </p>
                        <button type="submit">提交</button>
                    </form>
            )
        }
    });

    ReactDOM.render(
            <MyForm />,
        document.getElementById("div1")
    );
</script>
```
