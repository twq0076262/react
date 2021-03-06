# PureRenderMixin


如果你的React组件的渲染函数是“纯粹的”（换句话说，当传给它同样的props和state，它渲染出同样的效果），在某些场景下，你可以利用这个插件来极大地提升性能。

例如：

```
var PureRenderMixin = require('react').addons.PureRenderMixin;
React.createClass({
  mixins: [PureRenderMixin],

  render: function() {
    return <div className={this.props.className}>foo</div>;
  }
});
```

在底层，该插件实现了[shouldComponentUpdate](/react/docs/component-specs.html#updating-shouldcomponentupdate)，在这里面，它比较当前的props、state和接下来的props、state，当两者相等的时候返回`false`。

> 注意：
>
> 仅仅是浅比较对象。如果对象包含了复杂的数据结构，深层次的差异可能会产生误判。仅用于拥有简单props和state的组件，或者当你知道很深的数据结构已经变化了的时候使用`forceUpdate()`。或者，考虑使用[immutable objects](http://facebook.github.io/immutable-js/)来帮助嵌套数据快速比较。
>
> 此外，`shouldComponentUpdate`会跳过更新整个组件子树。确保所有的子组件也是“纯粹的”。
