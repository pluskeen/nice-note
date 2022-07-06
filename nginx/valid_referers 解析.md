##### 前置知识
- 什么是 `referer` 请求头？
**referer 请求头包含了当前请求页面的来源地址**，即表示当前页面是通过这个来源页面里的链接进入的。
服务端一般使用 `referer` 请求头识别访问来源，可能会以此进行统计分析、日志记录以及缓存优化等。
![[referer请求头.png]]

- 利用 referer 防止盗链？
假设当一个 `HTTP` 请求头的 `referer` **字段中包含一些不正确(期望)的值**。
那么可以使用 nginx 的 `ngx_http_referer_module` 模块，**禁止这个请求访问站点**。
也就是常说的 nginx 防盗链，不过需要注意的是，**referer 请求头是可以伪造的**，因此这个模块并不能 `100%` 的阻止这类请求。
防盗链功能主要由模块中的 `valid_referers` 指令与 `$invalid_referer` 变量配合使用。

##### 指令介绍
语法：`valid_referers none | blocked | server_names | string ...;`
作用域：`server`，`location`

参数说明：
- `none` 表示请求头中不存在 `referer`
- `blocked` 表示请求头中存在 `referer`，但值不是以 `http(s)://` 开头的字符串，被防火墙或代理服务器修改删除。
- `server_names` 表示 `referer`字段值包含服务器名称。服务器名称指 `server` 作用域下设置的 `server_name`。
- `string` 代表自定义的服务器名称或 `URI` 前缀，开头或结尾可以有一个 `*`。还可以是正则表达式，第一个符号必须为 `~`，表达式将于 `http(s)://` 之后的文本匹配。

例子：

```nginx
valid_referers none blocked server_names
               *.example.com example.* www.example.org/galleries/
               ~\.google\.;

if ($invalid_referer) { return 403; }
```

变量 `$invalid_referer`，如果 `referer` 请求头字段值被认为有效，则为空字符串，否则为 `1`。

##### 匹配说明
- 直接在浏览器打开某 `URI`，匹配参数 `none` 或 `blocked`。
- 带有 `referer` 字段，但值不是以 `http(s)://` 开头的字符串，匹配参数 `blocked`。
- 在某网站内部跳转，`referer` 信息中会带有 `server_name`，匹配参数 `server_names`。
- 带有域外主机名的 `referer` 信息，表示请求是从其他网站页面里的链接跳转过来的，会尝试在设置的 `string` 规则中匹配。没有发现匹配，按照规则 `$invalid_referer` 变量被设置为 `1` ，最终服务器返回 `403` 状态码。

