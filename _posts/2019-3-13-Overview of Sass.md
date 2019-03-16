---
title: Overview of Sass
tags: [css,web]
categories: [css]
layout: post

---

* content
{:toc}

- [ref: Sass Guide](https://sass-lang.com/guide)
- [ref: Sass Reference](https://sass-lang.com/documentation/file.SASS_REFERENCE.html)


# Sass & Scss 简介

Sass和Scss是CSS的预处理技术，在CSS的基础上添加了一些新特性，比如变量（variables），嵌套（nesting），继承（inheritance）等，同时输出的文件是标准的CSS文件。

Sass和Scss的区别是，Scss保留了css的一些语法习惯，比如花括号和分号；而Sass相比Scss则更为简洁，使用换行取代分号，使用缩进取代花括号。另外还有一些符号上的不同，比如`@mixin`和`@include`。

需要注意的几点有：

- Sass对缩进有严格的控制，虽然没强制规定缩进用几个空格，但是相同等级的语句缩进应该相同；
- Sass中`:`后面一定要跟一个空格，否则会报错。

Scss:

```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```

Sass:

```sass
nav
  ul
    margin: 0
    padding: 0
    list-style: none

  li
    display: inline-block

  a
    display: block
    padding: 6px 12px
    text-decoration: none
```

# 安装和启动方式

首先需要安装Ruby环境。安装Ruby之后使用gem安装sass：

```bash
gem install sass
```

然后就可以用sass的各种功能了。比较常用的命令，第一种是直接将sass（或scss）文件转化为css文件：

```bash
sass input.scss output.css
```

第二种是持续监测sass（或scss）文件的变化，每次更改保存之后都更新目标css文件：

```bash
sass --watch input.scss:output.css #单个文件
sass --watch app/sass:public/stylesheets #多个文件的目录
```

# 基本语法

## Variables

使用变量的格式为`$variable-name:variable-value`。


```sass
$font-stack:    Helvetica, sans-serif
$primary-color: #333

body
  font: 100% $font-stack
  color: $primary-color
```

## Nesting

Nesting的主要作用是避免重复使用前缀。

Sass:

```sass
nav
  ul
    margin: 0
    padding: 0
    list-style: none

  li
    display: inline-block

  a
    display: block
    padding: 6px 12px
    text-decoration: none
```

转化为CSS文件后为：

```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav li {
  display: inline-block;
}
nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
```

## Partials

Partials表示可以将部分常用的sass片段单独放在一个文件里，以便于其他sass文件引用这些片段。和需要转化成css的sass文件不同，这些文件只应该用于其他sass文件中，因此**文件名前必须加下划线**，比如`_snippet.sass`，以避免将其生成为css文件。

## Import

Import的导入方式为：`@import snippet`，其中`snippet`是**不带下划线和后缀名**的名称。在这里，`snippet`指代的是名为`_snippet.sass`的文件。

_reset.sass

```sass
html,
body,
ul,
ol
  margin:  0
  padding: 0
```

base.sass

```sass
@import reset

body
  font: 100% Helvetica, sans-serif
  background-color: #efefef
```

## Mixins

Mixins表示的是函数。在scss中，使用`@mixin`声明函数，使用`@include`调用函数；在sass中，使用`=`声明函数，使用`+`调用函数。

Mixin可以不使用参数，起到替代的作用：

```scss
@mixin large-text {
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}
```

```scss
.page-title {
  @include large-text;
  padding: 4px;
  margin-top: 10px;
}
```

等同于：

```scss
.page-title {
  font-family: Arial;
  font-size: 20px;
  font-weight: bold;
  color: #ff0000;
  padding: 4px;
  margin-top: 10px; 
}
```

也可以使用参数：

```scss
@mixin sexy-border($color, $width) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}

p { @include sexy-border(blue, 1in); }
```

## Extend/Inheritance

Sass中声明一个需要被继承的片段要使用`%`，继承的时候使用`@extend`。继承的作用在于，当多条语句继承自一条语句的时候，这一条语句表示他们相同的部分，将以组选择器的方式出现在输出css中；而他们不同的部分则再分开声明。未被继承的语句因为未被使用，将不会出现在输出css中。

```sass
/* This CSS will print because %message-shared is extended. */
%message-shared
  border: 1px solid #ccc
  padding: 10px
  color: #333


// This CSS won't print because %equal-heights is never extended.
%equal-heights
  display: flex
  flex-wrap: wrap


.message
  @extend %message-shared


.success
  @extend %message-shared
  border-color: green


.error
  @extend %message-shared
  border-color: red


.warning
  @extend %message-shared
  border-color: yellow

```

将转化为：

```css
/* This CSS will print because %message-shared is extended. */
.message, .success, .error, .warning {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  border-color: green;
}

.error {
  border-color: red;
}

.warning {
  border-color: yellow;
}

```

## Operators

Sass中可以使用`+`, `-`, `*`, `/`, 和 `%`用于基本运算，运算出的结果会直接输出到css文件中。

Sass:

```scss
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 600px / 960px * 100%;
}

aside[role="complementary"] {
  float: right;
  width: 300px / 960px * 100%;
}
```

css:

```css
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 62.5%;
}

aside[role="complementary"] {
  float: right;
  width: 31.25%;
}

```

