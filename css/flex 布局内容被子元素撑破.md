##### 问题描述
使用 `flex` 布局，经常会遇到 `flex` 容器被子元素撑大的问题。

```html
<div class="container">
	<div class="item">
		<div class="child"></div>
	</div>
	<div class="item">
		<div class="child"></div>
	</div>
	<div class="item">
		<div class="child"></div>
	</div>
</div>
```

```css
.container {
	display: flex;
	width: 500px;
	height:50px;
	background: green;
}
.item {
	border: 1px solid olive;
}
.child {
	width: 250px;
}
```

上面代码会出现下图的现象，子元素已经超出了绿色块的父元素。

![[flex布局子元素撑大父元素.png]]

##### 解决办法
给 `Flex Item` 容器设置 `overflow:hidden` 或者 `min-width: 0`  属性可以解决。

##### 问题探究
通过阅读[标准](https://www.w3.org/TR/css-flexbox-1/#min-size-auto)，可以得到如下信息：
- 在非滚动容器中，主轴方向 `Flex Item` 的最小尺寸基于内容的最小尺寸，此时 `min-width` 的值是 `auto`。
- 对于滚动容器，`min-width` 的值是 `0`（默认讨论水平布局）

结合已知信息，当 `item` 内容 `child` 的宽度是 `250px` 时，`Flex Item` 的最小尺寸就是 `250px`，肯定会超出父元素 `container`。按照标准规范，给 `item` 设置 `min-width: 0`  ，就可以使 `item` 按照预期平分 `500px` 的宽度。

规范也说明了在滚动容器中，`min-width` 是 `0`，给 `item` 增加 `overflow: auto` 或者`overflow: hidden` 一样可以达到目的。