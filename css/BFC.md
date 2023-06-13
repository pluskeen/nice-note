### 什么是BFC

官方定义：BFC（Block Formatting Context）块格式化上下文， 是 Web 页面的可视 CSS 渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

通俗理解：BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。


### 如何创建一个BFC

常用的方式有以下几种：

- 浮动元素（元素的 `float` 不是 `none`，指定 `float` 为 `left` 或者 `right` 就可以创建 BFC）。
- 绝对定位元素（元素的 `position` 为 `absolute` 或 `fixed`）。
- `display: inline-block`，`display: table-cell`，`display: flex`，`display: inline-flex`。
- `overflow` 指定除了 `visible` 的值。


### BFC有什么特点

- 在 BFC 中，块级元素从顶端开始垂直地一个接一个的排列。（即便不在BFC里块级元素也会垂直排列）
- 如果两个块级元素属于同一个 BFC，它们的上下 `margin` 会重叠，以较大的为准。但是如果两个块级元素分别在不同的 BFC 中，它们的上下边距就不会重叠了，而是两者之和。
- BFC 的区域不会与浮动的元素区域重叠，也就是说不会与浮动盒子产生交集，而是紧贴浮动边缘。
- 计算 BFC 的高度时，浮动元素也参与计算。BFC 可以包含浮动元素。（利用这个特性可以清除浮动）
- BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。


### BFC有什么用

##### 解决外边距折叠问题

外边距折叠只会发生在属于同一BFC的块级元素之间。

```html
<div class="div1"></div>
<div class="div2"></div>
```

```css
.div1 {
	width: 100px;
	height: 100px;
	background-color: green;
	margin-bottom: 10px;
}

.div2 {
	width: 100px;
	height: 100px;
	background-color: red;
	margin-top: 20px;
}
```

设置第一个 `div` 的 `margin-bottom: 10px`，第二个 `div` 的 `margin-top: 20px`，可以看到两个盒子最终的边距是 `20px`，是两者之中较大的一个。这就是外边距重叠的问题。

为了解决这个问题，可以让这两个 `div` 分属于不同的 BFC，或者只要把其中一个 `div` 放到 BFC 中就可以。原因是：BFC 就是页面上的一个隔离的独立容器，容器里面的元素不会对外边产生影响。

```html
<div class="wrapper">
	<div class="div1"></div>
</div>
<div class="div2"></div>
```

```css
.wrapper{
	/* 开启BFC */
	overflow: hidden;
}

.div1 {
	width: 100px;
	height: 100px;
	background-color: green;
	margin-bottom: 10px;
}

.div2 {
	width: 100px;
	height: 100px;
	background-color: red;
	margin-top: 20px;
}
```


##### 制作两栏布局

利用 BFC 的区域不会与浮动的元素区域重叠特性来处理。

```html
<div class="wrap">  
	<div class="left"></div>  
	<div class="right">  
		我是长文字！我是长文字！我是长文字！我是长文字！我是长文字！我是长文字！  
	</div>  
</div>
```

```css
.wrap {  
	width: 198px;  
}

.left {
	width: 70px;  
	height: 60px;  
	background-color: green;  
	float: left;
}

.right {
	height: 100px;
	background-color: red;
	overflow: hidden; /* 创建BFC */
}
```

上面代码实现了，左边宽度固定，右边宽度自适应的两栏布局。

如果第二个盒子不开启 BFC，会造成第二个盒子在第一个盒子的下方，被覆盖掉一部分，但文字不会被覆盖：

![[BFC-float-文字环绕.png]]

是因为 `float` 当初设计的时候就是为了**使文本围绕在浮动对象的周围**。


##### 清除元素内部的浮动

这里清除浮动的意思并不是清除元素的浮动属性，而是清除设置了浮动属性之后给别的元素带来的影响。例如给子元素设置浮动，那么父元素的高度就撑不开了。

BFC 有一个特性：计算 BFC 的高度时，浮动元素也参与计算，利用这个特性可以清除浮动。

```html
<div class="div1">
  <div class="son1">a</div>
  <div class="son2">b</div>
</div>
```

```css
.div1 {
  width: 150px;
  border: 1px solid red;

  /*使用BFC来清除浮动*/
  overflow: hidden;
}

.son1, .son2 {
  width: 100px;
  height: 100px;
  background-color: blue;
  float: left;
}

.son2 {
  background-color: greenyellow;
}
```

上面代码中，父元素高度会撑开，消除了子元素浮动带来的影响。

