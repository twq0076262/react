# 类名操作


`classSet()`是一个简洁的工具，用于简单操作DOM中的`class`字符串。

这里是一个常见的场景，处理方式中没有使用`classSet()`：

```
// inside some `<Message />` React component
render: function() {
  var classString = 'message';
  if (this.props.isImportant) {
    classString += ' message-important';
  }
  if (this.props.isRead) {
    classString += ' message-read';
  }
  // 'message message-important message-read'
  return <div className={classString}>Great, I'll be there.</div>;
}
```

这会很快变得单调乏味，因为指定类名的代码很难阅读，并且容易出错。`classSet()`解决了这个问题：

```
render: function() {
  var cx = React.addons.classSet;
  var classes = cx({
    'message': true,
    'message-important': this.props.isImportant,
    'message-read': this.props.isRead
  });
  // same final string, but much cleaner
  return <div className={classes}>Great, I'll be there.</div>;
}
```

当使用`classSet()`的时候，传递一个对象，对象上的键是你需要或者不需要的CSS类名。对应真值的键将会成为结果字符串的一部分。

`classSet`也允许传递一些类名作为参数，然后拼接这些类名：

```
render: function() {
  var cx = React.addons.classSet;
  var importantModifier = 'message-important';
  var readModifier = 'message-read';
  var classes = cx('message', importantModifier, readModifier);
  // Final string is 'message message-important message-read'
  return <div className={classes}>Great, I'll be there.</div>;
}
```

没有更多需要钻研的字符串拼接！
