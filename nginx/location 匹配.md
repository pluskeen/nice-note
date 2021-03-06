##### 语法规则
```nginx
location [ = | ~ | ~* | ^~ ] uri { ... }
location @name { ... }
```

`location` 关键字，后面跟着可选的修饰符，后面是要匹配的字符，花括号中是要执行的操作。

###### 修饰符

-   `=` 表示精确匹配。只有请求的url路径与后面的字符串完全相等时，才会命中。
-   `~` 表示该规则是使用正则定义的，区分大小写。
-   `~*` 表示该规则是使用正则定义的，不区分大小写。
-   `^~` 表示如果该符号后面的字符是最佳匹配，采用该规则，不再进行后续的查找。

##### 匹配过程

对请求的 `url` 序列化。例如，对已编码的 `url` 进行解码，去除 `url` 中多个相连的 `/`，解析url中的 `.`，`..` 等。这一步是匹配的前置工作。

`location` 有两种表示形式，一种是使用前缀字符，一种是使用正则。如果是正则的话，前面有 `~` 或 `~*` 修饰符。

具体的匹配过程如下：

首先检查使用前缀字符定义的 `location`，选择最长匹配的项并记录下来。

如果找到了精确匹配的 `location`，也就是使用了 `=` 修饰符的 `location`，结束查找，使用它的配置。

然后按顺序查找使用正则定义的 `location`，如果匹配则停止查找，使用它定义的配置。

如果没有匹配的正则 `location`，则使用前面记录的最长匹配前缀字符 `location`。

基于以上的匹配过程，我们可以得到以下两点启示：

1.  **使用正则定义的 `location` 在配置文件中出现的顺序很重要**。因为找到第一个匹配的正则后，查找就停止了，后面定义的正则就是再匹配也没有机会了。
2.  **使用精确匹配可以提高查找的速度**。例如经常请求 `/` 的话，可以使用 `=` 来定义location。

##### 示例

接下来以一个例子来具体说明一下匹配过程。

假如有下面的一段配置文件：

```nginx
location = / {
    [ configuration A ]
}

location / {
    [ configuration B ]
}

location /user/ {
    [ configuration C ]
}

location ^~ /images/ {
    [ configuration D ]
}

location ~* \.(gif|jpg|jpeg)$ {
    [ configuration E ]
}
```

请求 `/` 精准匹配 A，不再往下查找。

请求 `/index.html` 匹配 B。首先查找匹配的前缀字符，找到最长匹配是配置 B，接着又按照顺序查找匹配的正则。结果没有找到，因此使用先前标记的最长匹配，即配置 B。

请求 `/user/index.html` 匹配 C。首先找到最长匹配 C，由于后面没有匹配的正则，所以使用最长匹配 C。

请求 `/user/1.jpg` 匹配 E。首先进行前缀字符的查找，找到最长匹配项 C，继续进行正则查找，找到匹配项 E。因此使用 E。

请求 `/images/1.jpg` 匹配 D。首先进行前缀字符的查找，找到最长匹配 D。但是，特殊的是它使用了 `^~` 修饰符，不再进行接下来的正则的匹配查找，因此使用 D。这里，如果没有前面的修饰符，其实最终的匹配是 E。

请求 `/documents/about.html` 匹配 B。因为 B 表示任何以 `/` 开头的 `url` 都匹配。在上面的配置中，只有 B 能满足，所以匹配 B。

###### location @name的用法

`@` 用来定义一个命名 `location`。主要用于内部重定向，不能用来处理正常的请求。其用法如下：

```nginx
location / {
    try_files $uri $uri/ @custom
}

location @custom {
    # ...do something
}
```

上例中，当尝试访问 `url` 找不到对应的文件就重定向到我们自定义的命名 `location` `custom`。

值得注意的是，==命名 location 中不能再嵌套其它的命名 location==。

##### `url`尾部的 `/` 需不需要

关于 `url` 尾部的 `/` 有三点也需要说明一下。第一点与 `location` 配置有关，其他两点无关。

1.  `location` 中的字符有没有 `/` 都没有影响。也就是说 `/user/` 和 `/user` 是一样的。
2.  如果 `url` 结构是 `https://domain.com/` 的形式，尾部有没有`/`都不会造成重定向。因为浏览器在发起请求的时候，默认加上了`/`。虽然很多浏览器在地址栏里也不会显示`/`。这一点，可以访问[百度](https://www.baidu.com/)验证一下。
3.  如果 `url` 的结构是`https://domain.com/some-dir/`。尾部如果缺少`/`将导致重定向。因为根据约定， `url` 尾部的 `/` 表示目录，没有 `/` 表示文件。所以访问 `/some-dir/` 时，服务器会自动去该目录下找对应的默认文件。如果访问 `/some-dir` 的话，服务器会先去找 `some-dir` 文件，找不到的话会将 `some-dir` 当成目录，重定向到 `/some-dir/`，去该目录下找默认文件。

##### 总结

`location` 的配置有两种形式，前缀字符和正则。查找匹配的时候，先查找前缀字符，选择最长匹配项，再查找正则。正则的优先级高于前缀字符。

正则的查找是按照在配置文件中的顺序进行的。因此正则的顺序很重要，建议越精细的放的越靠前。

使用 `=` 精准匹配可以加快查找的顺序，如果根域名经常被访问的话建议使用 `=`。