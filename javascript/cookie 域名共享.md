`cookie` 在不同域名之间是无法共享的，即跨域问题。

在设置 `cookie` 时，省略 `domain` 参数，默认为当前域名。

`domain` 参数可以设置父域名以及自身，但不能设置其它域名，包括子域名，否则 `cookie` 不起作用。

如果 `cookie` 设置为顶级域名，则全部的域名，包括顶级域名、二级域名、三级域名等，都可以共享该 `cookie`。

如果 `cookie` 设置为当前域名，则当前域名及其下面的所有子域名可以共享该 `cookie`。
