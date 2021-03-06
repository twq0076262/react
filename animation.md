# 动画


React为动画提供一个`ReactTransitonGroup`插件组件作为一个底层的API，一个`ReactCSSTransitionGroup`来简单地实现基本的CSS动画和过渡。

## 高级API：`ReactCSSTransitionGroup`

`ReactCSSTransitionGroup`是基于`ReactTransitionGroup`的，在React组件进入或者离开DOM的时候，它是一种简单地执行CSS过渡和动画的方式。这个的灵感来自于优秀的[ng-animate](http://www.nganimate.org/)库。

### 快速开始

`ReactCSSTransitionGroup`是`ReactTransitions`的接口。这是一个简单的元素，包含了所有你对其动画感兴趣的组件。这里是一个例子，例子中我们让列表项淡入淡出。

```
var ReactCSSTransitionGroup = React.addons.CSSTransitionGroup;

var TodoList = React.createClass({
  getInitialState: function() {
    return {items: ['hello', 'world', 'click', 'me']};
  },
  handleAdd: function() {
    var newItems =
      this.state.items.concat([prompt('Enter some text')]);
    this.setState({items: newItems});
  },
  handleRemove: function(i) {
    var newItems = this.state.items;
    newItems.splice(i, 1);
    this.setState({items: newItems});
  },
  render: function() {
    var items = this.state.items.map(function(item, i) {
      return (
        <div key={item} onClick={this.handleRemove.bind(this, i)}>
          {item}
        </div>
      );
    }.bind(this));
    return (
      <div>
        <button onClick={this.handleAdd}>Add Item</button>
        <ReactCSSTransitionGroup transitionName="example">
          {items}
        </ReactCSSTransitionGroup>
      </div>
    );
  }
});
```
> 注意：
>
> 你必须为`ReactCSSTransitionGroup`的所有子级提供[`键`属性](/react/docs/multiple-components.html#dynamic-children)，即使只渲染一项。这就是React确定哪一个子级插入了，移除了，或者停留在那里。

在这个组件当中，当一个新的项被添加到`ReactCSSTransitionGroup`，它将会被添加`example-enter`类，然后在下一时刻被添加`example-enter-active` CSS类。这是一个基于`transitionName` prop的约定。

你可以使用这些类来触发一个CSS动画或者过渡。例如，尝试添加这段CSS代码，然后插入一个新的列表项：

```
.example-enter {
  opacity: 0.01;
  transition: opacity .5s ease-in;
}

.example-enter.example-enter-active {
  opacity: 1;
}
```

你将注意到，当你尝试移除一项的时候，`ReactCSSTransitionGroup`保持该项在DOM里。如果你正使用一个带有插件的未压缩的React构建版本，你将会看到一条警告：React期待一次动画或者过渡发生。那是因为`ReactCSSTransitionGroup`保持你的DOM元素一直在页面上，直到动画完成。尝试添加这段CSS代码：

```
.example-leave {
  opacity: 1;
  transition: opacity .5s ease-in;
}

.example-leave.example-leave-active {
  opacity: 0.01;
}
```

### 一组动画必须要挂载了才能生效

为了能够给它的子级应用过渡效果，`ReactCSSTransitionGroup`必须已经挂载到了DOM。下面的例子不会生效，因为`ReactCSSTransitionGroup`被挂载到新项，而不是新项被挂载到`ReactCSSTransitionGroup`里。将这个与上面的[快速开始](#getting-started)部分比较一下，看看有什么差异。

```
  render: function() {
    var items = this.state.items.map(function(item, i) {
      return (
        <div key={item} onClick={this.handleRemove.bind(this, i)}>
          <ReactCSSTransitionGroup transitionName="example">
            {item}
          </ReactCSSTransitionGroup>
        </div>
      );
    }, this);
    return (
      <div>
        <button onClick={this.handleAdd}>Add Item</button>
        {items}
      </div>
    );
  }
```

### 让一项或者零项动起来（Animating One or Zero Items）

虽然在上面的例子中，我们渲染了一个项目列表到`ReactCSSTransitionGroup`里，`ReactCSSTransitionGroup`的子级可以是一个或零个项目。这使它能够让一个元素实现进入和离开的动画。同样，你可以通过移动一个新的元素来替换当前元素。随着新元素的移入，当前元素移出。例如，我们可以由此实现一个简单的图片轮播器：

```
var ReactCSSTransitionGroup = React.addons.CSSTransitionGroup;

var ImageCarousel = React.createClass({
  propTypes: {
    imageSrc: React.PropTypes.string.isRequired
  },
  render: function() {
    return (
      <div>
        <ReactCSSTransitionGroup transitionName="carousel">
          <img src={this.props.imageSrc} key={this.props.imageSrc} />
        </ReactCSSTransitionGroup>
      </div>
    );
  }
});
```

### 禁用动画

如果你想，你可以禁用`入场`或者`出场`动画。例如，有些时候，你可能想要一个`入场`动画，不要`出场`动画，但是`ReactCSSTransitionGroup`会在移除DOM节点之前等待一个动画完成。你可以给`ReactCSSTransitionGroup`添加`transitionEnter={false}`或者`transitionLeave={false}` props来禁用这些动画。

> 注意：
>
> 当使用`ReactCSSTransitionGroup`的时候，没有办法通知你在过渡效果结束或者在执行动画的时候做一些复杂的运算。如果你想要更多细粒度的控制，你可以使用底层的`ReactTransitionGroup` API，该API提供了你自定义过渡效果所需要的函数。

## 底层的API：`ReactTransitionGroup`

`ReactTransitionGroup`是动画的基础。它可以通过`React.addons.TransitionGroup`得到。当子级被添加或者从其中移除（就像上面的例子）的时候，特殊的生命周期函数就会在它们上面被调用。

### `componentWillEnter(callback)`

在组件被添加到已有的`TransitionGroup`中的时候，该函数和`componentDidMount()`被同时调用。这将会阻塞其它动画触发，直到`callback`被调用。该函数不会在`TransitionGroup`初始化渲染的时候调用。

### `componentDidEnter()`

该函数在传给`componentWillEnter`的`callback`函数被调用之后调用。

### `componentWillLeave(callback)`

该函数在子级从`ReactTransitionGroup`中移除的时候调用。虽然子级被移除了，`ReactTransitionGroup`将会使它继续在DOM中，直到`callback`被调用。

### `componentDidLeave()`

该函数在`willLeave` `callback`被调用的时候调用（与`componentWillUnmount`是同一时间）。

### 渲染一个不同的组件

默认情况下`ReactTransitionGroup`渲染一个`span`。你可以通过提供一个`component` prop来改变这种行为。例如，以下是你将如何渲染一个`<ul>`：

```
<ReactTransitionGroup component="ul">
  ...
</ReactTransitionGroup>
```

每一个React能渲染的DOM组件都是可用的。但是，`组件`并不需要是一个DOM组件。它可以是任何你想要的React组件；甚至是你自己已经写好的！

> 注意：
>
> v0.12之前，当使用DOM组件的时候，`组件` prop需要是一个指向`React.DOM.*`的引用。既然组件简单地传递到了`React.createElement`，它必须是一个字符串。组装的组件必须传递构造函数。

任何额外的、用户定义的属性将会成为已渲染的组件的属性。例如，以下是你将如何渲染一个带有css类的`<ul>`：

```
<ReactTransitionGroup component="ul" className="animated-list">
  ...
</ReactTransitionGroup>
```
