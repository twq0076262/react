# 测试工具集


`React.addons.TestUtils`使得在你选择的测试框架中测试React组件变得简单（我们使用[Jest](http://facebook.github.io/jest/)）。

### 模拟

```
Simulate.{eventName}(DOMElement element, object eventData)
```

模拟事件在DOM节点上派发，附带可选的`eventData`事件数据。**这可能是在`ReactTestUtils`中最有用的工具。**

使用示例：

```
var node = this.refs.input.getDOMNode();
React.addons.TestUtils.Simulate.click(node);
React.addons.TestUtils.Simulate.change(node, {target: {value: 'Hello, world'}});
React.addons.TestUtils.Simulate.keyDown(node, {key: "Enter"});
```

`Simulate`有一个方法适用于每个事件，这些事件都是React能识别的。

### renderIntoDocument

```
ReactComponent renderIntoDocument(ReactComponent instance)
```

把一个组件渲染成一个在文档中分离的DOM节点。**这个函数需要DOM。**

### mockComponent

```
object mockComponent(function componentClass, string? mockTagName)
```

传递一个虚拟的组件模块给这个方法，给这个组件扩充一些有用的方法，让组件能够被当成一个React组件的仿制品来使用。这个组件将会变成一个简单的`<div>`（或者是其它标签，如果`mockTagName`提供了的话），包含任何提供的子节点，而不是像往常一样渲染出来。

### isElementOfType

```
boolean isElementOfType(ReactElement element, function componentClass)
```

如果`element`是一个类型为React `componentClass`的React元素，则返回true。

### isDOMComponent

```
boolean isDOMComponent(ReactComponent instance)
```

如果`instance`是一个DOM组件（例如`<div>`或者`<span>`），则返回true。

### isCompositeComponent

```
boolean isCompositeComponent(ReactComponent instance)`
```

如果`instance`是一个合成的组件（通过`React.createClass()`创建），则返回true。

### isCompositeComponentWithType

```
boolean isCompositeComponentWithType(ReactComponent instance, function componentClass)
```

如果`instance`是一个合成的组件（通过`React.createClass()`创建），此组件的类型是React `componentClass`，则返回true。

### findAllInRenderedTree

```
array findAllInRenderedTree(ReactComponent tree, function test)
```

遍历`tree`中所有组件，搜集`test(component)`返回true的所有组件。就这个本身来说不是很有用，但是它可以为其它测试提供原始数据。

### scryRenderedDOMComponentsWithClass

```
array scryRenderedDOMComponentsWithClass(ReactComponent tree, string className)
```

查找组件的所有实例，这些实例都在渲染后的树中，并且是带有`className`类名的DOM组件。

### findRenderedDOMComponentWithClass

```
ReactComponent findRenderedDOMComponentWithClass(ReactComponent tree, string className)
```

类似于`scryRenderedDOMComponentsWithClass()`，但是它只返回一个结果，如果有其它满足条件的，则会抛出异常。

### scryRenderedDOMComponentsWithTag

```
array scryRenderedDOMComponentsWithTag(ReactComponent tree, string tagName)
```

在渲染后的树中找出所有组件实例，并且是标签名字符合`tagName`的DOM组件。

### findRenderedDOMComponentWithTag

```
ReactComponent findRenderedDOMComponentWithTag(ReactComponent tree, string tagName)
```

类似于`scryRenderedDOMComponentsWithTag()`，但是它只返回一个结果，如果有其它满足条件的，则会抛出异常。

### scryRenderedComponentsWithType

```
array scryRenderedComponentsWithType(ReactComponent tree, function componentClass)
```

找出所有组件实例，这些组件的类型为`componentClass`。

### findRenderedComponentWithType

```
ReactComponent findRenderedComponentWithType(ReactComponent tree, function componentClass)
```

类似于`scryRenderedComponentsWithType()`，但是它只返回一个结果，如果有其它满足条件的，则会抛出异常。
