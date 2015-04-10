# 公开组件功能

 [组件之间的交互](http://facebook.github.io/react/tips/communicate-between-components.html)还有另一种(不常见的)方法：对父组件要调用的子组件简单的公开方法。

以一个待办事项列表为例，点击后该列表会被删除。如果只剩下一个未完成的待办事项，给它添加动画：

``` 
var Todo = React.createClass({
  render: function() {
    return <div onClick={this.props.onClick}>{this.props.title}</div>;
  },
  //this component will be accessed by the parent through the `ref` attribute
  animate: function() {
    console.log('Pretend %s is animating', this.props.title);
  }
});
var Todos = React.createClass({
  getInitialState: function() {
    return {items: ['Apple', 'Banana', 'Cranberry']};
  },
  handleClick: function(index) {
    var items = this.state.items.filter(function(item, i) {
      return index !== i;
    });
    this.setState({items: items}, function() {
      if (items.length === 1) {
        this.refs.item0.animate();
      }
    }.bind(this));
  },
  render: function() {
    return (
      <div>
        {this.state.items.map(function(item, i) {
          var boundClick = this.handleClick.bind(this, i);
          return (
            <Todo onClick={boundClick} key={i} title={item} ref={'item' + i} />
          );
        }, this)}
      </div>
    );
  }
});
React.render(<Todos />, mountNode);
```

或者，你可能已经通过把 `isLastUnfinishedItem` 道具传递给 `todo` 实现了上述操作，并让它在 `componentDidUpdate` 中检查道具，然后对其本身进行动画控制；然而，如果你传递不同的道具来控制动画，这很快就会变得混乱。

