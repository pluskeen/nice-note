##### sessionStorage
- 不同浏览器不共享
- 标签页关闭会清除当前标签页 sessionStorage，浏览器关闭会清空所有页面 sessionStorage
- 主动打开一个新窗口或者新标签页不共享
- 刷新当前页面，或者通过 `location.href`、`window.open` 打开的新标签页，会共享
- 点击有 `target="_blank"` 的 `a` 标签，打开的新标签页，不会共享

##### localStorage
- 不同浏览器不共享
- 受同源策略限制，可以共享的页面仅限同一协议、同一域名、同一端口，子域名与主域名间无法共享
