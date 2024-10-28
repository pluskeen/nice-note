想在 safari 浏览器上设置一个全屏容器时，设置 CSS `height: 100vh;`，发现浏览器的底部工具栏会挡住底下的一部分，而且会出现滚动条，只有工具栏隐藏或者向上滚动的时候才能看到被遮挡的部分。

解决方法：

```js
const appHeight = () => {  
	document.body.style.setProperty('height', `${window.innerHeight}px`);  
}  
  
window.addEventListener("resize", appHeight);
// 当初始的 HTML 文档被完全加载和解析完成之后，DOMContentLoaded 事件被触发，而无需等待样式表、图像和子框架的完全加载
window.addEventListener("DOMContentLoaded", appHeight);  
```

若通过 `window.innerHeight` 获取的高度需要在其他 CSS 中使用到，可以这样：

```js
const appHeight = () => {  
	const doc = document.documentElement
    doc.style.setProperty('--app-height', `${window.innerHeight}px`)
}
```

`appHeight` 函数会新增一个全局的 CSS 自定义变量 `--app-height`，可以在项目其他地方使用，例如：

```css
/* appHeight 函数生成的 css 自定义变量 */
/* :root 和 html 是一样的，权重比 html 高 */
:root {
   --app-height: 100%;
}

html,
body {
    padding: 0;
    margin: 0;
    overflow: hidden;
    width: 100vw;
    height: 100vh;
    height: var(--app-height);
}
```


### 额外知识

`load` 事件在整个页面及所有依赖资源如样式表和图片都已完成加载时触发。它与 [`DOMContentLoaded`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/DOMContentLoaded_event "DOMContentLoaded") 不同，后者只要页面 DOM 加载完成就触发，无需等待依赖资源的加载。