1. 对于undefined、任意的函数以及symbol三个特殊的值分别作为对象属性的值、数组元素、单独的值时JSON.stringify返回不同的结果。
```js
// undefined、任意的函数以及symbol作为对象属性时JSON.stringify跳过对它们进行序列化。
const data = {
	a: "aaa",
	b: undefined,
	c: Symbol("dd"),
	fn: function () {},
};
JSON.stringify(data)
// "{"a":"aaa"}"


// undefined、任意的函数以及symbol作为数组元素值时，JSON.stringify会将它们序列化为null。
JSON.stringify(["aaa", undefined, function fn() {}, Symbol('dd')])
// "["aaa",null,null,null]"


// undefined、任意的函数以及symbol作为单独的值进行序列化都会返回undefined。
JSON.stringify(() => {}) // undefined
JSON.stringify(undefined) // undefined
JSON.stringify(Symbol('aa')) // undefined
```
2. 由于JSON.stringify会跳过对象中属性值为undefined、任意的函数以及symbol的属性，所以非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
3. 转换对象中如果包含toJSON函数，该函数返回什么值，序列化结果就是什么值，并且忽略其他属性的值。
```js
JSON.stringify({
 say: "hello JSON.stringify",
 toJSON: function() {
 	return "today i learn";
 }
})
// "today i learn"
```
4. JSON.stringify会正常序列化Date的值。
```js
JSON.stringify(new Date()) 
// "2020-05-08T03:18:27.804Z"
```
5. NaN、Infinity和null都会序列化出null。
```js
JSON.stringify(null) // null
JSON.stringify(NaN) // null
JSON.stringify(Infinity) // null
```
6. 仅会序列化对象中可枚举的属性。
```js
JSON.stringify(
 Object.create(null, {
	 x: { value: "json", enumerable: false },
	 y: { value: "stringify", enumerable: true },
 })
);
// "{"y":"stringify"}"
```
7. 对包含循环引用的对象（对象间相互引用，形成无线循环）序列化，会抛出错误。
8. 所有以symbol为属性键的属性都会被忽略，即便replacer参数中强制指定返回它们。
```js
JSON.stringify({ [Symbol("a")]: "stringify" }); // {}
JSON.stringify({ [Symbol.for("json")]: "stringify" }, function (k, v) {
 if (typeof k === "symbol") {
 	return v;
 }
});
// undefined
```