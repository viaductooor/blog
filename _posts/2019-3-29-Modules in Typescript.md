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

> A note about terminology: It’s important to note that in TypeScript 1.5, the nomenclature has changed. “Internal modules” are now “namespaces”. “External modules” are now simply “modules”, as to align with ECMAScript 2015’s terminology, (namely that module X { is equivalent to the now-preferred namespace X {).



在ECMAScript1.5之前，modules被区分成了external modules和internal modules；external modules也就是单独的文件，internal modules则是在一个文件内分隔命名空间（功能模块）的一个方案。之前声明internal modules的方方法是使用`module`关键字，现在改为使用`namespace`关键字。

## Declare a namespace

Namespace的声明方式有点类似接口或者类，只是里面的内容不同。namespace里面包含的是类、接口、变量等内容：

```ts
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    const lettersRegexp = /^[A-Za-z]+$/;
    const numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

//调用方式是namespace_name.declaration_name
let validators: { [s: string]: Validation.StringValidator; } = {};
```

namespace可以跨文件定义。也就是说，各个文件中的相同命名空间中的内容可以最后集中到一起，互相调用完成一些功能。为了告诉各个文件他们参考的namespace（而不是他们自己定义的namespace），需要添加reference标签来指向目标namespace所在的文件。

*Validation.ts*

```ts
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```

*LettersOnlyValidator.ts*

```ts
/// <reference path="Validation.ts" />
namespace Validation {
    export class LettersOnlyValidator implements StringValidator {
        //...
    }
}
```

*ZipCodeValidator.ts*

```ts
/// <reference path="Validation.ts" />
namespace Validation {
    export class ZipCodeValidator implements StringValidator {
        //...
    }
}
```

*Test.ts*

```ts
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();
```

## export as namespace

`export as namespace L;`这句代码的意思是在全局作用域中创建一个变量`L`用于访问当前Module。比如可以在其他（该Module外）直接调用：

```js
L.func1();
L.func2();
```

但是一般不会这样调用，为了避免污染全局作用域。使用一般的导入方式就可以了，比如：

```js
import * as L from 'the_module';
L.func1();
L.func2();
```


