---
title: Extra Markdown Features in Kramdown
categories: [jekyll]
tags: [markdown, jekyll]
layout: post
---

* catalog
{:toc}

ref: https://kramdown.gettalong.org/syntax.html

# Footnotes

Footnotes类似于文献综述的脚标，使用`[^number]`在文段中插入脚标，使用`[^number]: Content`来添加详细解释。<mark>不管详细解释放在文档的哪个位置，最后都会重新排序然后放在文档的末尾。</mark>

```
This is a text with a
footnote[^1].

[^1]: And here is the definition.
```

# Abbreviations

当鼠标放在使用了这个标记的文字上时，会出现这个部分的全称，或其他解释内容，比如HTML。使用这个功能的时候<mark>不需要再原文中添加符号，只需要在文章中的其他部分声明</mark>。使用方法为：

```
This is an HTML
example.

*[HTML]: Hyper Text Markup Language
```

*[HTML]: Hyper Text Markup Language

# Inline Attribute Lists

这个功能相当于给Markdown里面的标记添加HTML的属性，因为使用Kramdown之后原来的Markdown标记实际是转化为HTML元素了。

```
This is *red*{: style="color: red"}.
```

上面的部分实际转化成Html语言为：

```html
<p>This is <em style="color: red">red</em>.</p>
```

跟上面的`*red*`一样，很多前后加标记的功能都是可以直接在后面加Inline Attribute的，比如：

- 加粗
- Inline code
- 斜体
- 链接

另外一些以块为单位标记的，比如引用块，普通的文段，或者标题，需要在另外一行添加相关属性：

```
A simple paragraph with an ID attribute.
{: #para-one}

> A blockquote with a title
{:title="The blockquote title"}
{: #myid}
```

这个功能在jekyll里面有一个很常见的应用，就是指定目录：

```
* catalog
{:toc}
```