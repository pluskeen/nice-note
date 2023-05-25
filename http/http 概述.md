**_超文本传输协议_**（HTTP）是一个用于传输超媒体文档（例如 HTML）的[应用层](https://zh.wikipedia.org/wiki/%E5%BA%94%E7%94%A8%E5%B1%82)协议。它是为 Web 浏览器与 Web 服务器之间的通信而设计的，但也可以用于其他目的。HTTP 遵循经典的[客户端—服务端模型](https://zh.wikipedia.org/wiki/%E4%B8%BB%E5%BE%9E%E5%BC%8F%E6%9E%B6%E6%A7%8B)，客户端打开一个连接以发出请求，然后等待直到收到服务器端响应。HTTP 是[无状态协议](http://zh.wikipedia.org/wiki/%E6%97%A0%E7%8A%B6%E6%80%81%E5%8D%8F%E8%AE%AE)，这意味着服务器不会在两个请求之间保留任何数据（状态）。


##### http 发展历史

HTTP/0.9
只接受 GET 请求方法
没有在通讯中指出 HTTP 协议版本号
不支持请求头
服务器发送完数据后，立马关闭通讯连接，例关闭 TCP 连接

HTTP/1.0
增加很多命令，例 POST, OPTIONS, HEAD, PUT, DELETE
增加响应状态码 status code
在通讯中增加指定 HTTP 版本号和请求头
增加多字符集支持
增加缓存，权限等内容

HTTP/1.1
增加持久连接，且默认采用
增加管道方式，同时发送多个请求，串行处理请求，必须等待上一个请求得到响应后，才执行下一个请求
请求头增加 host 行，同一台物理服务器，可以运行多个web服务，通过 host 的值来设别，请求的是哪个 web 服务

HTTP/2.0
所有数据以二进制传输
管道方式优化，可并发处理多个请求
头信息压缩
服务器推送，需要配置，未收到请求，就把资源推送给浏览器

HTTP/3.0
传输层部分使用 [QUIC (en-US)](https://developer.mozilla.org/en-US/docs/Glossary/QUIC "Currently only available in English (US)") 而不是 [TCP](https://developer.mozilla.org/zh-CN/docs/Glossary/TCP)


参见 mdn 上文章 [HTTP 的发展](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP)


##### 名词说明

URI uniform resource identifier
统一资源标识符
用于标识某一互联网资源名称的字符串
是 URL 和 URN 的统称

URL uniform resource locator
统一资源定位符

URN  uniform resource name
永久统一资源定位符
