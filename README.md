# React 中文版

React 是 Facebook 推出的一个用来构建用户界面的 JavaScript 库。特点：React不是一个MVC框架、React不使用模板、响应式更新非常简单、HTML5仅仅是个开始。

[快速入门](getting-started.md) 

[下载 React v0.13.0](http://facebook.github.io/react/downloads/react-0.13.1.zip)

本教程部分内容来源于 [React 中文网](http://reactjs.cn/react/index.html)

React 官网：<http://facebook.github.io/react/index.html>


|更新日期    |更新内容
|----------|--------------------
|2015-04-10|React 中文版发布
## 仅仅是UI
许多人使用React作为MVC架构的V层。 尽管React并没有假设过你的其余技术栈， 但它仍可以作为一个小特征轻易地在已有项目中使用

## 虚拟DOM
React为了更高超的性能而使用虚拟DOM作为其不同的实现。 它同时也可以由服务端Node.js渲染 － 而不需要过重的浏览器DOM支持

## 数据流
React实现了单向响应的数据流，从而减少了重复代码，这也是它为什么比传统数据绑定更简单。

### 一个简单的组件
React组件通过一个`render()`方法，接受输入的参数并返回展示的对象。 
以下这个例子使用了JSX，它类似于XML的语法
输入的参数通过`render()`传入组件后，将存储在`this.props`

#### JSX是可选的，并不强制要求使用。
点击 "Compiled JS" 可以看到JSX编译之后的JavaScript代码

Live JSX Editor

```
    var HelloMessage = React.createClass({
      render: function() {
        return <div>Hello {this.props.name}</div>;
      }
    });
 
    React.render(<HelloMessage name="John" />, mountNode)
Hello John
```

Compiled JS

```
var HelloMessage = React.createClass({displayName: "HelloMessage",
  render: function() {
    return React.createElement("div", null, "Hello ", this.props.name);
  }
});

React.render(React.createElement(HelloMessage, {name: "John"}), mountNode);
```
###一个有状态的组件

除了接受输入数据（通过 `this.props` ），组件还可以保持内部状态数据（通过 `this.state` ）。当一个组件的状态数据的变化，展现的标记将被重新调用 `render()` 更新。

Live JSX Editor

```
var Timer = React.createClass({
  getInitialState: function() {
    return {secondsElapsed: 0};
  },
  tick: function() {
    this.setState({secondsElapsed: this.state.secondsElapsed + 1});
  },
  componentDidMount: function() {
    this.interval = setInterval(this.tick, 1000);
  },
  componentWillUnmount: function() {
    clearInterval(this.interval);
  },
  render: function() {
    return (
      <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
    );
  }
});
 
React.render(<Timer />, mountNode);
```
Compiled JS

```
var Timer = React.createClass({displayName: "Timer",
  getInitialState: function() {
    return {secondsElapsed: 0};
  },
  tick: function() {
    this.setState({secondsElapsed: this.state.secondsElapsed + 1});
  },
  componentDidMount: function() {
    this.interval = setInterval(this.tick, 1000);
  },
  componentWillUnmount: function() {
    clearInterval(this.interval);
  },
  render: function() {
    return (
      React.createElement("div", null, "Seconds Elapsed: ", this.state.secondsElapsed)
    );
  }
});

React.render(React.createElement(Timer, null), mountNode);
```

### 一个应用程序
通过使用`props`和`state`, 我们可以组合构建一个小型的Todo程序。
下面例子使用`state`去监测当前列表的项以及用户已经输入的文本。 尽管事件绑定似乎是以内联的方式，但他们将被收集起来并以事件代理的方式实现。

```
Live JSX EditorCompiled JS

var TodoList = React.createClass({
  render: function() {
    var createItem = function(itemText) {
      return <li>{itemText}</li>;
    };
    return <ul>{this.props.items.map(createItem)}</ul>;
  }
});
var TodoApp = React.createClass({
  getInitialState: function() {
    return {items: [], text: ''};
  },
  onChange: function(e) {
    this.setState({text: e.target.value});
  },
  handleSubmit: function(e) {
    e.preventDefault();
    var nextItems = this.state.items.concat([this.state.text]);
    var nextText = '';
    this.setState({items: nextItems, text: nextText});
  },
  render: function() {
    return (
      <div>
        <h3>TODO</h3>
        <TodoList items={this.state.items} />
        <form onSubmit={this.handleSubmit}>
          <input onChange={this.onChange} value={this.state.text} />
          <button>{'Add #' + (this.state.items.length + 1)}</button>
        </form>
      </div>
    );
  }
});
 
React.render(<TodoApp />, mountNode);
```
Compiled JS

```
var TodoList = React.createClass({displayName: "TodoList",
  render: function() {
    var createItem = function(itemText) {
      return React.createElement("li", null, itemText);
    };
    return React.createElement("ul", null, this.props.items.map(createItem));
  }
});
var TodoApp = React.createClass({displayName: "TodoApp",
  getInitialState: function() {
    return {items: [], text: ''};
  },
  onChange: function(e) {
    this.setState({text: e.target.value});
  },
  handleSubmit: function(e) {
    e.preventDefault();
    var nextItems = this.state.items.concat([this.state.text]);
    var nextText = '';
    this.setState({items: nextItems, text: nextText});
  },
  render: function() {
    return (
      React.createElement("div", null, 
        React.createElement("h3", null, "TODO"), 
        React.createElement(TodoList, {items: this.state.items}), 
        React.createElement("form", {onSubmit: this.handleSubmit}, 
          React.createElement("input", {onChange: this.onChange, value: this.state.text}), 
          React.createElement("button", null, 'Add #' + (this.state.items.length + 1))
        )
      )
    );
  }
});

React.render(React.createElement(TodoApp, null), mountNode);
```


### 一个使用外部插件的组件
React是灵活的，并且提供方法允许你跟其他库和框架对接。
下面例子展现了一个案例，使用外部库Markdown实时转化textarea的值。

Live JSX Editor

```
var converter = new Showdown.converter();
 
var MarkdownEditor = React.createClass({
  getInitialState: function() {
    return {value: 'Type some *markdown* here!'};
  },
  handleChange: function() {
    this.setState({value: this.refs.textarea.getDOMNode().value});
  },
  render: function() {
    return (
      <div className="MarkdownEditor">
        <h3>Input</h3>
        <textarea
          onChange={this.handleChange}
          ref="textarea"
          defaultValue={this.state.value} />
        <h3>Output</h3>
        <div
          className="content"
          dangerouslySetInnerHTML={{
            __html: converter.makeHtml(this.state.value)
          }}
        />
      </div>
    );
  }
});
 
React.render(<MarkdownEditor />, mountNode);

```
Compiled JS

```
var converter = new Showdown.converter();

var MarkdownEditor = React.createClass({displayName: "MarkdownEditor",
  getInitialState: function() {
    return {value: 'Type some *markdown* here!'};
  },
  handleChange: function() {
    this.setState({value: this.refs.textarea.getDOMNode().value});
  },
  render: function() {
    return (
      React.createElement("div", {className: "MarkdownEditor"}, 
        React.createElement("h3", null, "Input"), 
        React.createElement("textarea", {
          onChange: this.handleChange, 
          ref: "textarea", 
          defaultValue: this.state.value}), 
        React.createElement("h3", null, "Output"), 
        React.createElement("div", {
          className: "content", 
          dangerouslySetInnerHTML: {
            __html: converter.makeHtml(this.state.value)
          }}
        )
      )
    );
  }
});

React.render(React.createElement(MarkdownEditor, null), mountNode);
```


[快速入门](getting-started.md) 

[下载 React v0.13.0](http://facebook.github.io/react/downloads/react-0.13.1.zip)



