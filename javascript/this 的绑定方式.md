##### `this` 的 5 种绑定方式

1. 默认绑定（非严格模式下 `this` 指向全局对象，严格模式下函数内的 `this` 指向 `undefined`）
2. 隐式绑定（当函数引用有上下文对象时, 如 `obj.foo()` 的调用方式, `foo` 内的 `this` 指向 `obj`）
3. 显示绑定（通过 `call` 或者 `apply` 方法直接指定 `this` 的绑定对象，如 `foo.call(obj)`）
4. `new` 构造函数绑定，`this` 指向新生成的对象
5. 箭头函数，`this` 指向的是定义该函数时，外层环境中的 `this`，**箭头函数的 `this` 在定义时就决定了，不能改变**

##### 题目一

```js
"use strict"
var a = 10 // var 定义的 a 变量挂载到 window 上
function foo () {
  console.log('this1', this)  // undefined，严格模式下函数内的 this 指向 undefined
  console.log(window.a)  // 10
  console.log(this.a)  // 报错，Cannot read properties of undefined (reading 'a')
}
console.log('this2', this)  // window
foo()
```

##### 题目二

```js
let a = 10
const b = 20
function foo () {
  console.log(this.a)  // undefined
  console.log(this.b)  // undefined
}  
foo()
console.log(window.a) // undefined
```

`let` 或 `const` 定义的变量是不会被绑定到 `window` 上的，所以此时会打印出三个 `undefined`。

##### 题目三

```js
var a = 1  
function foo () {  
  var a = 2  
  console.log(this)  // window，非严格模式下 `this` 指向全局对象  
  console.log(this.a) // 1  
}  
foo()
```

##### 题目四

```js
var obj2 = {
	a: 2,
	foo1: function () {
	  console.log(this.a) // 2
	},
	foo2: function () {
	  setTimeout(function () {
		console.log(this) // window
		console.log(this.a) // 3
	  }, 0)
	}
}
var a = 3

obj2.foo1()
obj2.foo2()
```

将函数作为参数传递时，会被隐式赋值，回调函数丢失 `this` 绑定，这时候 `setTimeout` 中函数内的 `this` 指向 `window`。

##### 题目五

```js
var obj = {
 name: 'obj',
 foo1: () => {
   console.log(this.name) // window
 },
 foo2: function () {
   console.log(this.name) // obj
   return () => {
     console.log(this.name) // obj
   }
 }
}

var name = 'window'  
obj.foo1()  
obj.foo2()()
```

**箭头函数内的 `this` 是由外层作用域决定的**

1）对于 `obj.foo1()` 的调用，它的外层作用域是 `window`，对象 `obj` 当然不属于作用域了（作用域只有全局作用域、函数作用域、块级作用域），所以会打印出 `window`。

2）`obj.foo2()()`，首先会执行 `obj.foo2()`，这不是个箭头函数，所以它里面的 `this` 是调用它的 `obj` 对象，因此第二个打印为 `obj`，而返回的匿名函数是一个箭头函数，**它的 `this` 由外层作用域决定**，那也就是它的 `this` 会和 `foo2` 函数里的 `this` 一样，第三个打印也是 `obj`。

##### 题目六

```js
var a = 1
function foo () {
	var a = 2
	function inner () {
		console.log(this.a)
	}
	inner()
}
foo()
```

`inner` 函数内部，`this` 还是指向 `window`。