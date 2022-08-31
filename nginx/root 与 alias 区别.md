`root` 与 `alias` 主要区别在于 nginx 如何解释 `location` 后面的 `uri`，这会使两者分别以不同的方式将请求映射到服务器文件上。  

`root` 的处理结果是：**root路径** + **location路径**。
`alias` 的处理结果是：使用 **alias路径** 替换 **location路径**。

`alias` 是一个目录别名的定义，仅能用于 `location` 上下文。
`root` 则是最上层目录的定义。

`alias` 后面必须要用 `/` 结束，否则会找不到文件，而 `root` 则可有可无。

通过样例理解：

```nginx
location ^~ /path/some/ {
	root /data/www;
}
```

根据上面的配置，当请求 `http://xxx.com/path/some/logo.png` 时，将会返回服务器上的 `/data/www/path/some/logo.png` 文件，即 `/data/www` + `/path/some/`。

```nginx
location ^~ /path/some/ {
	alias /data/www;
}
```

根据上面的配置，当请求 `http://xxx.com/path/some/logo.png` 时，将会返回服务器上的 `/data/www/logo.png` 文件，即 `/data/www`。

