---
title: Javascript 常用代码段
tags: [javascript]
categories: [javascript]
layout: post
---

* content
{:toc}

# React 中需要注意的属性名

## 与Html标签冲突的属性名

React中使用了JSX语法，允许在代码中直接使用部分Html片段（或以类似的风格创建对象），而Javascript中的部分关键字和Html中的一些关键字冲突，在解析上可能会引起歧义，因此使用了这些代替方法：

1. htmlFor，取代Html原来的for
2. className，取代Html中原来的class

# React中使用内联样式表

React中可以在标签内添加样式，但是样式表是以对象的方式传值的，因此常用的方式是：

```
<div style={ {height:100+'px'} }></div>
```

样式外面有两层括号，外面一层指示这是一段代码，里面一层表示这是一个对象。

样式中的键必须符合Javascript的要求（字母、下划线或$开头，后续符号可为字母、下划线、数字或$），因此所有包含`-`符号的键都将改写为驼峰式命名，比如`max-height`将被替代为`maxHeight`。

<mark>不要直接在自定义的React组件中使用style属性</mark>，所有自定义组件中的属性都将通过props的形式调用，因此这里添加的style不会映射到对应的DOM组件中，除非在定义这个组件的时候使用props将属性传递到DOM组件上：

```
render(){
    return (
        <div style={this.props.style}></div>
    );
}
```

可以将样式对象独立出来：

```
var divStyle = {
  color: 'white',
  backgroundImage: 'url(' + imgUrl + ')',
  WebkitTransition: 'all', // note the capital 'W' here
  msTransition: 'all' // 'ms' is the only lowercase vendor prefix
};

ReactDOM.render(<div style={divStyle}>Hello World!</div>, mountNode);
```
