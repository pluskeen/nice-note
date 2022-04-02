##### 指令介绍
语法：`try_files file ... uri`，`try_files file ... =code`
作用域：`server`，`location`

说明：
- 按指定的 `file` 顺序查找存在的文件，并使用第一个找到的文件进行请求处理。
- 查找路径是按照给定的 `root` 或 `alias` 为根路径来查找的。
- 如果给出的 `file` 都没有匹配到，则会进行内部重定向到最后一个参数 `uri`。
- 只有最后一个参数能引发内部重定向，之前的参数只设置内部 `uri` 的指向。
- 最后一个参数是回退 `uri` 且必须存在，否则会出现内部500错误。
- [[Location 匹配#location name的用法|命名的 location]] 可以使用在最后一个参数中。
- 若最后一个参数是 `=code` 形式，在 `file` 都没有匹配到时，会返回 `code` 的响应码。

##### 举例说明
```nginx
location / {
	root      /home/nx/dist;
	try_files $uri $uri/ /index.html?$query_string;
	expires   7d;
}
```
当用户请求 `http://localhost/example` 时，这里的 `$uri` 就是 `/example`，`try_files` 会到硬盘中尝试查找这个文件。

若存在名为 `/$root/example`（ `$root` 就是给定的 `/home/nx/dist`）的文件，就直接把这个文件的内容发送给用户。

若目录中没有 `example` 文件，就看 `$uri/`，也就是看有没有名为 `/$root/example/` 的目录。

还找不到，就会内部重定向到 `try_files` 的最后一个参数 `/index.html`，发起内部子请求，也就是相当于 nginx 发起一个 HTTP 请求到 `http://localhost/index.html`。

`$query_string` 可以获取到 `url` 中 `?` 后面的参数，不包括 `?` 本身。

##### 常见错误
```nginx
location ~.*\.(gif|jpg|png|jpeg) {
	root      /web/root;
	try_files /static/$uri $uri;
}
```
原意图是访问 `http://example.com/test.jpg` 时先去检查 `/web/root/static/test.jpg` 是否存在，不存在就取 `/web/wwwroot/test.jpg`。

但由于最后一个参数是一个内部重定向，所以并不会检查 `/web/root/test.jpg`是否存在，只要第一个路径不存在就会重定向，然后再进入这个 `location` 造成死循环。结果出现 `500 Internal Server Error`。