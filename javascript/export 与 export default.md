##### export
`export` 命令规定了模块对外的接口。一个模块就是一个独立的文件。该文件内部的所有变量，外部都无法获取。如果希望外部能读取模块内部某个变量，就必须使用 `export` 关键字输出该变量。

`export` 的写法有两种。

```js
// 第一种
export var firstName = 'Michael';
export var lastName = 'Jackson';
export function fn() {}

// 第二种
var firstName = 'Michael';
var lastName = 'Jackson';
function fn() {}

export { firstName, lastName, fn };
```

这两种写法是等价，推荐使用第二种，这样就可以在文件的尾部，一眼看清输出了哪些变量。

使用 `as` 关键字重命名。

```js
function fn1() {}
function fn2() {}

export {
	fn1 as rename1,
	fn2 as rename2,
	fn2 as renameAgian
}
```

相同的变量可以用不同的重命名名字输出两次。

特别容易弄错的是，`export` 命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。

```js
const m = 1;
function f() {}

export 1;
export m;
export f;
```

上面的写法都会报错，因为没有提供对外接口。第一个 `export` 直接输出 `1`，第二个通过变量 `m`，还是直接输出 `1`，`1` 只是一个值，不是接口，第三个 `function` 也必须遵守这样的写法。

`export` 语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。

```js
export var foo = 1;
setTimeout(() => foo = 2, 500)
```

上面代码输出的变量 `foo`，会在 500 毫秒之后变成 `2`。这一点与 CommonJS 规范完全不同。CommonJS 模块输出的是值得缓存，不存在动态更新。

`export` 命令需要处于模块的顶层，如果处于块级作用域内，就会报错。

```js
function foo() {
  export default 'bar' // SyntaxError
}
foo()
```

##### export default
为了让模块使用者更快的上手，就要用到 export default 命令，为模块指定默认输出。

```js
// export-default.js
export default function () {
  console.log('foo');
}
```

上面的模块，默认输出了一个匿名函数。其他模块加载该模块时，可以为该匿名函数指定任意名字。

```js
// another.js
import customName from './export-default';
customName(); // 'foo'
```

上面代码的 `import` 命令，可以使用任意名字指向 `export-default.js` 输出的方法，这时就不需要知道原模块输出的函数名。这是 `import` 命令后面，不使用大括号。

export default 命令也可以用在非匿名函数前。

```js
// export-default.js
export default function foo() {
  console.log('foo');
}

// 或者写成

function foo() {
  console.log('foo');
}

export default foo;
```

上面代码中，`foo` 函数的名字，在模块外部是无效的，加载的时候，视同匿名函数。

比较下 `export` 与 `export default` 的输出。

```js
// 第一组
export default function crc32() { // 输出
  // ...
}

import crc32 from 'crc32'; // 输入

// 第二组
export function crc32() { // 输出
  // ...
};

import {crc32} from 'crc32'; // 输入
```

在上面的两种写法中，使用 `export default` 时，对应的 `import` 语句不需要使用大括号。在一个模块中只能有一个 `export default` 命令，所以 `import` 命令后面才不用加大括号，因为只可能唯一对应 `export default` 命令。

本质上， `export default` 就是输出一个叫做 `default` 的变量或者方法，然后允许你为他任意取名字。正因如此，它后面就不能再跟变量声明语句。

```js
// 正确
export var a = 1;

// 正确
var a = 1;
export default a; // 将 a 的值赋给变量 default

// 错误
export default var a = 1;
```

因为 `export default` 本质是将后面的值，赋给 `default` 变量，所以可以直接将一个值写在 `export default` 后面。

```js
// 正确
export default 42;

// 报错
export 42;
```

可以同时输出默认方法和其他接口。

```js
export default function (obj) {
  // ···
}

export function each(obj, iterator, context) {
  // ···
}
// forEach 为 each 的重命名
export { each as forEach };
```

对应上面代码的 import 语句如下。

```js
import _, { each, forEach } from 'module';
```