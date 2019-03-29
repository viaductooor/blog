---
title: Modules & Namespaces in Typescript
tags: [javascript,typescript]
categories: [javascript]
layout: post
---

* content
{:toc}

ref: https://www.typescriptlang.org/docs/handbook/modules.html

# Modules

默认情况下，Module内部的变量、函数和类等成员都只能在Module内使用，除非使用`export`表达式声明这个成员可以在Module外被使用；相对的，使用这个Module外的变量、函数等需要用`import`表达式来导入。

# Export 与 Import

## Export a declaration

Declaration是指变量、函数、接口等声明，使用`export`可以简单地将他们导出：

```typescript
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

## Export statements

上面的代码等同于：

```ts
interface StringValidator {
    isAcceptable(s: string): boolean;
}
export {StringValidator};
```

这里面的`{StringValidator}`是一个简单的表达式，表示其他地方引用这个成员的时候要使用`StringValidator`这个名称。可以使用`as`表达式来改变名称：

```ts
export {StringValidator as sv};
```

## Default exports

一个Module里面只能有一个default export。使用方法和Javascript相同。

```ts
declare let $: JQuery;
export default $;
```

## export = 与 import = require()

`export =` 和`import = require()`是成对出现的，和`export default`一样，一个Module只能使用这个方法导出一个类（class）、接口（interface）、命名空间（namespace）、方法（function）或者枚举（enum）。

*ZipCodeValidator.ts*

```ts
class ZipCodeValidator {
    //...
}
export = ZipCodeValidator;
```

*Test.ts*

```ts
import zip = require("./ZipCodeValidator");
```

<mark>在导入Module的成员的时候，如果这个成员不是使用default导出的，则必须加`{}`，如果是default导出的则不用加。</mark>

例如：

```js
import React,{Component} from 'react'
```

这里面React是default export的，而Component不是。

# Namespaces

