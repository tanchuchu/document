---
title: const常量基础详解
data: 2020-04-23 22:00
categories: 
- js

tags:
- es6
- js
- const

toc: true

---

### 什么是const常量

定义常量,常量就是不变的值，用在函数，变量，数组，指针类型说明前（如：const int a=10;）表示的a值永远是10.
<!--more-->
### const详细
#### 声明const
const声明一个只读的常量。一旦声明，常量的值就不能改变。
```javascript
const PI = 3.1415;
PI // 3.1415

PI = 3;
// TypeError: Assignment to constant variable.
```
> 上面代码表明改变常量的值会报错。

const声明的变量不得改变值，这意味着，`const`一旦声明变量，就必须立即初始化，不能留到以后赋值。
```javascript
const foo;
// SyntaxError: Missing initializer in const declaration
```
> 上面代码表示，对于const来说，只声明不赋值，就会报错。

`const`的作用域与`let`命令相同：只在声明所在的块级作用域内有效。
```javascript
if (true) {
  const MAX = 5; 
}

MAX // Uncaught ReferenceError: MAX is not defined
```
`const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
```javascript
if (true) {
  console.log(MAX); // ReferenceError
  const MAX = 5;
}
```
> 上面代码在常量MAX声明之前就调用，结果报错。

`const`声明的常量，也与`let`一样不可重复声明。
```javascript
var message = "Hello!";
let age = 25;

// 以下两行都会报错
const message = "Goodbye!";
const age = 30;
```
对于复合类型的变量，变量名不指向数据，而是指向数据所在的地址。`const`命令只是保证变量名指向的地址不变，
并不保证该地址的数据不变，所以将一个对象声明为常量必须非常小心。
```javascript
const foo = {};
foo.prop = 123;

foo.prop
// 123
foo = {}; // TypeError: "foo" is read-only
```
>上面代码中，常量 foo 储存的是一个地址，这个地址指向一个对象。
不可变的只是这个地址，即不能把 foo 指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。

下面是另一个例子。
```javascript
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```
>上面代码中，常量a是一个数组，这个数组本身是可写的，但是如果将另一个数组赋值给a，就会报错。
如果真的想将对象冻结，应该使用Object.freeze方法。
```javascript
const foo = Object.freeze({});
// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```
> 上面代码中，常量foo指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。

除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。
```javascript
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, value) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```
>ES5只有两种声明变量的方法：`var`命令和`function`命令。ES6除了添加`let`和`const`命令，
后面章节还会提到，另外两种声明变量的方法：`import`命令和`class`命令。所以，
ES6一共有6种声明变量的方法。

#### 顶层对象的属性(window)

顶层对象，在浏览器环境指的是 window 对象，在 Node 指的是 global 对象。ES5 之中，
顶层对象的属性与全局变量是等价的。
```javascript
window.a = 1;
a // 1

a = 2;
window.a // 2
```
> 上面代码中，顶层对象的属性赋值与全局变量的赋值，是同一件事。

顶层对象的属性与全局变量挂钩，被认为是 JavaScript 语言最大的设计败笔之一。
这样的设计带来了几个很大的问题，首先是没法在编译时就报出变量未声明的错误，
只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；
其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；
最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。
另一方面，window 对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，
也是不合适的。

ES6 为了改变这一点，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，
依旧是顶层对象的属性；另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，
不属于顶层对象的属性。也就是说，从ES6开始，全局变量将逐步与顶层对象的属性脱钩。
```javascript
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```

>上面代码中，全局变量 a 由`var`命令声明，所以它是顶层对象的属性；
全局变量 b 由`let` 命令声明，所以它不是顶层对象的属性，返回`undefined`。

#### global 对象
ES5的顶层对象，本身也是一个问题，因为它在各种实现里面是不统一的。
- 浏览器里面，顶层对象是window，但 Node 和 Web Worker 没有window。
- 浏览器和 Web Worker 里面，self也指向顶层对象，但是Node没有self。
- Node 里面，顶层对象是global，但其他环境都不支持。

同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用`this`变量，但是有局限性。
- 全局环境中,`this`会返回顶层对象。但是，Node 模块和 ES6 模块中，`this` 返回的是当前模块。
- 函数里面的`this`,如果函数不是作为对象的方法运行，而是单纯作为函数运行，`this`会指向顶层对象。但是，严格模式下，这时`this`会返回`undefined`。
- 不管是严格模式，还是普通模式，`new Function('return this')()`，总是会返回全局对象。但是，如果浏览器用了`CSP（Content Security Policy，内容安全政策）`，那么`eval`、`new Function`这些方法都可能无法使用。

综上所述，很难找到一种方法，可以在所有情况下，都取到顶层对象。下面是两种勉强可以使用的方法。
```javascript
// 方法一
(typeof window !== 'undefined'
   ? window
   : (typeof process === 'object' &&
      typeof require === 'function' &&
      typeof global === 'object')
     ? global
     : this);

// 方法二
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }// Web Worker
  if (typeof window !== 'undefined') { return window; } // windew
  if (typeof global !== 'undefined') { return global; } // node环境
  throw new Error('unable to locate global object');
};
```
现在有一个提案，在语言标准的层面，引入 global 作为顶层对象。也就是说，在所有环境下，
global 都是存在的，都可以从它拿到顶层对象。

垫片库 system.global 模拟了这个提案，可以在所有环境拿到 global。

```javascript
// CommonJS的写法
require('system.global/shim')();

// ES6模块的写法
import shim from 'system.global/shim'; shim();
```
>上面代码可以保证各种环境里面，global对象都是存在的。
```javascript
// CommonJS的写法
var global = require('system.global')();

// ES6模块的写法
import getGlobal from 'system.global';
const global = getGlobal();
```
> 上面代码将顶层对象放入变量global。

`globalThis` 提供了一个标准的方式来获取不同环境下的全局 `this`  对象（也就是全局对象自身）。
不像 `window` 或者 `self` 这些属性，它确保可以在有无窗口的各种环境下正常工作。
所以，你可以安心的使用 `globalThis`，不必担心它的运行环境。为便于记忆，你只需要记住，全局作用域中的
 `this` 就是 `globalThis`。

### 我的总结
1.const
- 本质：`const`定义一个常量 指向一个内存地址不变，但不能影响内存的数据变化（比如：复合型变量(数组，对象)）
- 和`let`一样有 `暂时性死区`,不能在声明前使用
- 没有变量提升

2.顶层对象属性
- 顶层对象的属性与全局变量是等价的 (window)
- es6有声明变量的方法，两种`var`,`function`声明全局变量，还有`let`,`const`,`class`,`import`  

3.Global对象
- 浏览器里面里使用`window`
- web Worker`self`
- Node 里使用`global`
- 三种环境都能用`globalThis`


