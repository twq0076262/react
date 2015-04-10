# 键控的片段

在大多数情况下,您可以使用 `key` 道具来指定你从 `render` 返回的元素的键。然而，这种做法在一种情况下是不成立的：如果你有两组需要重新排序的孩子，那么在不增加包装器元素的情况下没有办法给每组放置一个键。

也就是说，如果你有一个组件，例如：

``` 
var Swapper = React.createClass({
  propTypes: {
    // `leftChildren` and `rightChildren` can be a string, element, array, etc.
    leftChildren: React.PropTypes.node,
    rightChildren: React.PropTypes.node,
    swapped: React.PropTypes.bool
  }
  render: function() {
    var children;
    if (this.props.swapped) {
      children = [this.props.rightChildren, this.props.leftChildren];
    } else {
      children = [this.props.leftChildren, this.props.rightChildren];
    }
    return <div>{children}</div>;
  }
});
```

当你改变 `swapped` 道具时，子组件会卸载并重新安装，因为没有任何键标记了这两组子组件。

为了解决这个问题，你可以使用 `React.addons.createFragment` 给两组孩子设置键。

**ReactFragment React.addons.createFragment(object children)**

不是创建数组，我们写：

``` 
if (this.props.swapped) {
  children = React.addons.createFragment({
    right: this.props.rightChildren,
    left: this.props.leftChildren
  });
} else {
  children = React.addons.createFragment({
    left: this.props.leftChildren,
    right: this.props.rightChildren
  });
}
```

传递的对象的键(即，`left` 和 `right`)用来当作整个子组件组的键，并且对象的键的顺序是用于确定子组件呈现的顺序的。有了这个变化，两组孩子将在不被卸载的情况下，在 DOM 中正确地重新排序。

`createFragment` 的返回值应该被视为一个不透明的对象；你可以使用 `React.Children` 助手来在片段中循环，但不应该直接访问它。还要注意我们依靠 JavaScript 引擎保存对象枚举顺序，这不是由规范保证的，而是通过所有的主浏览器和非数字键对象的 VMs 实现的。

>**注意：**

>将来 `createFragment` 有可能被如下所示的 API 取代：

>
``` 
	return (
  		<div>
    		<x:frag key="right">{this.props.rightChildren}</x:frag>,
    		<x:frag key="left">{this.props.leftChildren}</x:frag>
  		</div>
	);
``` 
>
允许你直接在 JSX 中分配键，不用添加包装器元素。

