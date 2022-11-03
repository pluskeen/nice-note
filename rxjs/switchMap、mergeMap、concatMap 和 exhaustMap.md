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
-   有可能第二个表单值比前一个表单值在后端保存更快，这种情况，新的表单值将**不会**立即映射到 HTTP 请求
-   相反，concatMap 将等待先前的 HTTP Observable 完成，然后再将新的表单值映射到 HTTP Observable，订阅并触发下一次保存


##### merge 操作符

在某些需求中，希望将请求并行运行，而不是等待前面的请求完成后再执行。

![[rxjs-merge.png]]

上图是 `merge` 操作符的弹珠图，被合并的源 Observable 的值会立即输出，直到所有合并的源 Observable 完成，合并后的 Observable 才会完成。


##### mergeMap 操作符

如果我们将合并策略与高阶 Observable 映射的概念结合起来，我们就得到了 `mergeMap` 操作符。

![[rxjs-mergeMap.png]]

下面是 `mergeMap` 操作符的工作原理：
-   源 Observable 的每个值仍然被映射到内部 Observable 中，就像 `concatMap` 的情况一样
-   和 `concatMap` 一样，内部 Observable 也被 `mergeMap` 订阅
-   当内部 Observable 发出新值时，它们会立即反映在输出 Observable 中
-   与 `concatMap` 不同的是，在 `mergeMap` 的情况下，不必等待前一个内部 Observable 完成才能触发下一个内部 Observable
-   这意味着使用 `mergeMap` 可以让多个内部 Observables 随着时间的推移重叠，并行发射值，就像在图片中以红色突出显示的那样

回到表单输入的例子，如果选择了 `mergeMap` 操作符会发生什么：
```js
this.form.valueChanges
	.pipe(
		mergeMap(formValue => this.http.put(`/api/course/${courseId}`, formValue))
	)
	.subscribe(
		saveResult => ... handle successful save ...,
		err => ... handle save error ...
	);
```

假设用户相当快地输入数据。在网络日志中会看到多个保存请求并行运行：

![[mergeMap-demo.png]]

在这种需求下，并行请求是一个错误！在重负载下，这些请求可能会被乱序处理。


##### switch 操作符

顾名思义，就是切换的意思。在切换时，在新的 Observable 送出后直接处理新的 Observable 不管前一个 Observable 是否完成，每当有新的 Observable 送出就会直接把旧的 Observable 退订(unsubscribe)，永远只处理最新的 Observable。

```bash
click  : ---------c-c------------------c--..
		map(e => Rx.Observable.interval(1000))
source : ---------o-o------------------o--..
				   \ \                  \ ----0----1--... 
				   \  ----0----1----2----3----4--...
				    ----0----1----2----3----4--...
				     switch()
example: -----------------0----1----2---------0----1--...
```

从上面的时序图中，可以看到：
-   开始时，第一次点击后形成的 Observable 被退订了
-   时间来到第二次点击后的第 3 秒，又触发了一次点击，代表有新的 Observable 值发出
-   退订了第 3 秒发出的 Observable，最后只在处理最后一次点击后发出的 Observable 值


##### switchMap 操作符

```js
interval(3000)
	.pipe( switchMap(() => timer(0, 1000)) ) // 立马发出数字，并持续间隔 1 秒后自增
	.subscribe(data => { console.log(data); });
// 0 
// 1 
// 2 
// 0 (新事件发生，退订上一個 Observable) 
// 1 
// 2 
// ...
```

来源 Observable (`interval(0, 3000)`) 每次有新事件发生时，会产生新的 Observable (`timer(0, 1000)`)，如果上一次 Observable 没有完成，会被退掉订阅，「切换」成新的 Observable。因此每次都只会产生 `0, 1, 2` 的循环。

![[rxjs-switchMap.jpg]]

每次事件发生时，都会被换成另一个 Observable 并且订阅它，同时上一个 Observable 如果还没完成，会把它退订掉。

当我们需要关注在「新事件产生的流，过去的流不再重要时」，就可以考虑使用 `switchMap`。


##### exhaustMap 操作符

exhaust 有「力竭」的意思，可以把它理解成，来源 Observable 有新事件发生时，它是没有力气产生新的 Observable 的；也就是说当来源事件发生时，如果上一次转换的 Observable 尚未结束，就不会产生新的 Observable。

让我们直接用弹珠图来解释：

![[rxjs-exhaustMap.jpg]]

当来源事件 2 发生时，由于上一次转换后的 Observable 还没结束，因此新的 Observable 不会进行订阅，直接忽略掉；当来源事件 3 发生时，由于之前的 Observable 都结束了，因此新的 Observable 会进行订阅。

在用户可能重复多次触发 API 请求时，使用 exhaustMap ，可以在 API 返回(Observable 结束)前避免产生重复的 API 请求。