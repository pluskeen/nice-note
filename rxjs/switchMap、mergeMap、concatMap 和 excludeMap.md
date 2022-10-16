了解 `switchMap`、`mergeMap`、`concatMap` 和 `excludeMap` 这几个操作符的内部逻辑，才能在写代码时做出正确的选择。

想要弄懂它们，先得知道 `map`、`switch`、`merge`、`concat` 这几个操作符的一般用法。


##### map 操作符

![[rxjs-map.png]]

上图是 `map` 操作符的弹珠图，它从一个输入流（值为 1、2、3）中映射出一个输出流（值为 10、20、30）。
输出流的值是通过获取输入流的值，并将它们应用到一个函数中得出的：这个函数是将值乘以 10。

进阶一点的用法，可以将输入流的值，映射到一个 Observable 中，跟其他 Observable 一样，可以被单独订阅。
例如，在 Angular 的响应式表单中，提供了一个 `form.valueChanges` 的 Observable，它会在用户与表单交互时发出最新的表单值。

把它当成源 Observable，假设有通过后端保存表单值草稿的功能，可以这样写：

```javascript
form.valueChanges
	.subscribe(formValue => {
	
		const httpPost$ = this.http.put(`/api/course/${courseId}`, formValue);
		
		httpPost$
			.subscribe(
				res => ... handle successful save ...
				err => ... handle save error ...
			);
	});
```

上述代码中，当表单值有变化时，会发出最新的订阅，回调中，通过接口服务将最新的表单值传递给后端进行保存。

但是，这样的写法会导致代码中出现嵌套的订阅，这是需要避免的问题。因为不能保证后端会按顺序处理，所以需要确保前一次保存完成后，再发送后续的保存请求。


##### concat 操作符

为了实现顺序保存，可以使用 RxJS 中的 `concat` 操作符。它可以将两个 Observable 相互连接。

```js
const series1$ = of('a', 'b');

const series2$ = of('x', 'y');

const result$ = concat(series1$, series2$);

result$.subscribe(console.log);
```

在使用 `of` 函数创建两个 Observable `series1$` 和 `series2$` 之后，创建了第三个 Observable `result$`，它是连接 `series1$` 和 `series2$` 的结果。

在控制台中可以看到输出的结果：

```js
a
b
x
y
```

查看弹珠图，可以很好理解发生了什么：

![[rxjs-concat.png]]

请注意第一个 Observable 最后的值 `b` 后面的竖线，表示 Observable 完成的时间点。

按照时间表逐步分解这里发生的事情：

-   传递给 `concat()` 函数两个 Observable `series1$`、`series2$` 
-   `concat()` 将订阅第一个 Observable `series1$`，但**不订阅**第二个 Observable `series2$`（这对于理解串联至关重要）
-   `series1$` 发出值 `a`，该值立即在 `result$` Observable 中输出
-   注意 `series2$` 还没有发出值，因为它还没有被订阅
-   然后 `series1$` 将发出值 `b`，该值会反映在输出中
-   然后 `series1$` 将完成，只有在那之后 `concat()` 才会订阅 `series2$`
-   然后 `series2$` 里的值将在输出中体现，直到 `series2$` 完成
-   完成 `series2$` 后，`result$` Observable 也将完成

请注意，可以将任意数量的 Observable 传递给 `concat()`。

这个过程中的关键点在于，取第一个 Observable 的值使用，等待它完成，然后使用下一个 Observable，依此类推，直到所有 Observable 完成。

回到上述例子中，想要实现顺序保存，需要一个操作符来混合下面两个操作：
- 将每次更新的表单值，并映射到 `httpPost$` 中
- 通过 `concat()` 操作，将多个 `httpPost$` Observable 连接在一起，让其按顺序处理每次请求的结果。

结合上述过程，可以在 RxJS 中找到一个名为 `concatMap` 的操作符。


##### concatMap 操作符

使用 `concatMap` 操作符改写上面的代码：

```js
this.form.valueChanges
	.pipe(
		concatMap(formValue => this.http.put(`/api/course/${courseId}`, formValue))
	)
	.subscribe(
		saveResult => ... handle successful save ...,
		err => ... handle save error ...
	);
```

可以看到，不再有嵌套订阅出现。若在浏览器中运行，可以在浏览器的开发者工具 Network 选项卡中查看请求的顺序。

![[rxjs-concatMap-example.png]]

分析上面的网络日志图：
-   concatMap 将获取到的表单值转换为 HTTP Observable，称为内部 Observable
-   然后 concatMap 订阅内部 Observable 并将其输出发送到结果 Observable
-   第二个表单值可能比前一个表单值在后端保存更快
-   如果发生这种情况，新的表单值将**不会**立即映射到 HTTP 请求
-   相反，concatMap 将等待先前的 HTTP Observable 完成，然后再将新值映射到 HTTP Observable，订阅并触发下一次保存


并且，在实现需求过程中，可能还会遇到以下问题：
-   应该等待一个保存请求完成后再进行另一次保存吗？
-   应该同时进行多次保存吗？
-   应该取消正在进行的保存并开始新的保存吗？
-   是否应该在已经进行中时忽略新的保存尝试？