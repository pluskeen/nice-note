##### `display: none;`
- `DOM` 结构：浏览器不会渲染 `display` 属性为 `none` 的元素，不占据空间；
- 事件监听：无法进行 `DOM` 事件监听；
- 性能：动态改变此属性时会引起重排，性能较差；
- 继承：不会被子元素继承，毕竟子类也不会被渲染；
- `transition`：`transition` 不支持 `display`；
- 能否被 js 获取：可以。


##### `visibility: hidden;`
- `DOM` 结构：元素被隐藏，但是会被渲染不会消失，占据空间；
- 事件监听：无法进行 `DOM` 事件监听；
- 性能：动态改变此属性时会引起重绘，性能较高；
- 继承：会被子元素继承，子元素可以通过设置 `visibility: visible;` 来取消隐藏；
- `transition`：`visibility` 会立即显示，隐藏时会延时；
- 能否被 js 获取：可以。
