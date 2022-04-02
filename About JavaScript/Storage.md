#### sessionStorage
- 不同浏览器不共享
- 不同标签页，不同页面不共享
- 标签页关闭会清除当前标签页的sessionStorage，浏览器关闭会清空所有页面的sessionStorage

#### localStorage
- 不同浏览器不共享
- 受同源策略限制，可以共享的页面仅限同一协议、同一域名、同一端口，子域名与主域名间无法共享