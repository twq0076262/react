# 更多的关于Refs

从你的 render 方法中返回你的 UI 结构后，你会发现你想要“伸手”调用从 render 返回的组件实例的方法。通常来说，这样做对于通过你的应用程序制作数据流是没有必要的，因为 Reactive 数据流总是确保最新的 `props` 被发送到由 `render()` 输出的每个孩子中。但是在一些情况下，它仍然有可能是必要或有益的。

考虑这样一种情况，当你在把一个 `<input / >` 元素(存在于你的实例 sub-hierarchy 中)的值更新为一个空字符串后` `，想告诉它聚焦。

``` 
var App = React.createClass({
    getInitialState: function() {
      return {userInput: ''};
    },
    handleChange: function(e) {
      this.setState({userInput: e.target.value});
    },
    clearAndFocusInput: function() {
      this.setState({userInput: ''}); // Clear the input
      // We wish to focus the <input /> now!
    },
    render: function() {
      return (
        <div>
          <div onClick={this.clearAndFocusInput}>
            Click to Focus and Reset
          </div>
          <input
            value={this.state.userInput}
            onChange={this.handleChange}
          />
        </div>
      );
    }
  });
```

注意，在本例中，我们想要“告诉”输入一些东西——这些东西是它不能从道具中推断出的。在这个例子中，我们想要“告诉”它，现在它应该聚焦。然而，也有一些挑战。从 `render()` 返回的不是你 “子”组件的实际组成，它仅仅是在一个特定的实例中的子组件的*描述*——如果你愿意的话可以是一个快照。

>**注意：**

>
记住，从 `render()` 返回来的不是你*实际*绘制的子组件的实例。从 `render()` 返回来的仅仅是一个特定的时刻在你组成部分的 sub-hierarchy 中的子组件实例的*描述*。

这意味着你不应该“持有”从 `render()` 返回来的东西，并且指望它有任何的意义。

``` 
// counterexample: DO NOT DO THIS!
  render: function() {
    var myInput = <input />;          // I'm going to try to call methods on this
    this.rememberThisInput = myInput; // input at some point in the future! YAY!
    return (
      <div>
        <div>...</div>
        {myInput}
      </div>
    );
  }
```

在这个反例中，`<input/ >` 仅仅是 `<input/ >` 的描述。这个描述是用来为 `<input/ >` 创建一个*真正的* **支持实例**。

那么， 我们怎么访问 input的 *真正的* 支持实例呢?

## Ref 的字符串属性
React 支持一个非常特殊的属性，你可以附加到任何从 `render()` 输出的组件中。这个特殊的属性允许你涉及相应的任何从 `render()` 返回的**支持实例**。它总是保证成为适当的实例，在任何时候。

这个非常简单：

1. 给从 `render` 返回的东西分配 `ref` 属性，如：

``` 
<input ref="myInput" />
```

2. 在其他一些代码(典型的是事件处理程序的代码)，通过 `this.refs` 访问 **backing instance**，如：

``` 
this.refs.myInput
```

你可以通过调用 `React.findDOMNode(this.refs.myInput)` 直接访问组件的 DOM 节点。

## ref 回调属性 

`ref` 属性可以是一个回调函数，而不是一个名字。这个回调函数在组件安装后立即执行。被引用的组件作为一个参数传递，且回调函数可以立即使用这个组件，或保存供以后使用(或实现这两种行为)。

它与把 `ref` 属性分配给从 `render` 返回来的东西一样简单，如：

``` 
<input ref={ function(component){ React.findDOMNode(component).focus();} } />
```

## 完成的示例

``` 
var App = React.createClass({
    getInitialState: function() {
      return {userInput: ''};
    },
    handleChange: function(e) {
      this.setState({userInput: e.target.value});
    },
    clearAndFocusInput: function() {
      // Clear the input
      this.setState({userInput: ''}, function() {
        // This code executes after the component is re-rendered
        React.findDOMNode(this.refs.theInput).focus();   // Boom! Focused!
      });
    },
    render: function() {
      return (
        <div>
          <div onClick={this.clearAndFocusInput}>
            Click to Focus and Reset
          </div>
          <input
            ref="theInput"
            value={this.state.userInput}
            onChange={this.handleChange}
          />
        </div>
      );
    }
  });
```

在这个例子中，render 函数返回 `<input/ >` 实例的描述。但真正的实例是通过 `this.refs.theInput` 访问的。只要带有 `ref =“theInput”` 的子组件从 render 返回，`this.refs.theInput` 就会访问适当的实例。这甚至能在更高的级别(non-DOM)组件中实现，如 `<Typeahead ref = " myTypeahead " / >`。

## 总结

向一个特定的子实例发送消息，Refs 是一个很好的方式，而通过流动式接收 Reactive 的 `props`  和 `state` 的方式可能是不方便的。然而，对于你的应用程序中的流动数据来说，refs 应该不是你的首选抽象特性。默认情况下，为用例使用 Reactive 数据流并保存 `ref`s 本来就是无功无过的。

### 好处：

- 你可以在组件类中定义任何公共方法(如在 Typeahead 中的复位方法)，并通过 refs(如 ` this.refs.myTypeahead.reset()`)调用这些公共方法。
- 执行 DOM 测量几乎总是需要接触“本地”组件，如 `<input/ >`，并通过 ` React.findDOMNode(this.refs.myInput)` 访问它的底层 DOM 节点。Refs 是可行的可靠的方法之一。
- refs 自动为你 book-kept!如果子组件被摧毁，那么它的 ref 也被摧毁了。在这里不必担心内存(除非你为保留自己的 reference 做了一些疯狂的事)。

### 注意事项：

- *永远*不要访问组件的 render 方法内部的 refs——或任何组件的 render 方法正在 call satck 中运行时，也不要访问 refs。
- 如果你想保存 Google Closure Compiler Crushing 的弹性，确保永远不要访问作为字符串被指定的属性。这意味着如果你的 ref 被定义为 ` ref =“myRefString”`，你必须使用 ` this.refs['myRefString']` 来访问。
- 如果你还没有用 React 给几个应用程序编程，那么你通常首先会倾向于尝试使用 refs 来“让事情发生”在你的应用程序中。如果是这样的话，花点时间，更为慎重的思考一下 `state` 应该属于组件层次结构的什么位置。通常情况下，你会清楚地发现，“拥有”那个 state 的适当的位置是更高的层次结构中。把 state 放置在那里通常可以消除任何想要使用 `ref` 来“让事情发生”的现象——相反，数据流通常会实现你的目标。
