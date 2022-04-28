##### 基于名称的虚拟服务
nginx首先会决定由哪个*服务器*来处理请求，下面的配置有3个虚拟服务器都在监听`*:80`：
```nginx
server {
    listen      80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      80;
    server_name example.net www.example.net;
    ...
}

server {
    listen      80;
    server_name example.com www.example.com;
    ...
}
```
在此配置中，nginx仅测试请求的标头字段 **Host** 以确定应将请求路由器到哪个服务。如果其值与任何服务器名称都不匹配，或者请求根本不包含此标头字段，则nginx会将请求路由到该端口的默认服务器。在上面的配置中，默认服务器是第一个服务，也可以在`listen`指令中使用`default_server`参数来明确设置哪个服务是默认服务器：
```nginx
server {
    listen      80 default_server;
    server_name example.net www.example.net;
    ...
}
```

##### 如何防止处理未定义服务器名称的请求
如果不允许不带 **Host** 标头字段的请求，则可以定义丢弃该请求的服务器：
```nginx
server {
    listen      80;
    server_name "";
    return      444;
}
```
在这里，服务器名称设置为空字符串，该字符串将与没有 **Host** 标头字段的请求进行匹配，并且返回特殊的nginx的非标准代码444，以关闭连接。

##### 基于名称和基于IP的混合虚拟服务器
一个更复杂的配置，其中一些虚拟服务器侦听不同的地址：
```nginx
server {
    listen      192.168.1.1:80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      192.168.1.1:80;
    server_name example.net www.example.net;
    ...
}

server {
    listen      192.168.1.2:80;
    server_name example.com www.example.com;
    ...
}
```
在这种配置中，nginx首先根据服务器块的`listen`指令测试请求的IP地址和端口。然后，根据匹配的服务器块的`server_name`条目测试请求的 **Host** 标头字段 。如果找不到服务器名称，则默认服务器将处理该请求。例如，在192.168.1.1:80端口接收到`www.example.com`的请求，将由192.168.1.1:80端口的默认服务器处理。

如前所述，==默认服务器是侦听端口的属性==，并且可以为不同的端口定义不同的默认服务器：
```nginx
server {
    listen      192.168.1.1:80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      192.168.1.1:80 default_server;
    server_name example.net www.example.net;
    ...
}

server {
    listen      192.168.1.2:80 default_server;
    server_name example.com www.example.com;
    ...
}
```
[How nginx processes a request](http://nginx.org/en/docs/http/request_processing.html#how_to_prevent_undefined_server_names)