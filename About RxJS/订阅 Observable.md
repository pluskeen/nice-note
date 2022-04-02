订阅 `Observable` 可以传递三种类型的值：
- `next` 通知： 发送一个值，比如数字、字符串、对象，等等。
- `error` 通知： 发送一个 `JavaScript` 错误 或 异常。
- `complete` 通知： 不再发送任何值。

`next` 通知是最重要，也是最常见的类型：它们表示传递给观察者的实际数据。
`error` 和 `complete` 通知可能只会在 `Observable` 执行期间发生一次，并且只会执行其中的一个。

在 `Observable` 执行中, 可能会发送零个到无穷多个 `next` 通知。如果发送的是 `error` 或 `complete` 通知的话，那么之后不会再发送任何通知了。