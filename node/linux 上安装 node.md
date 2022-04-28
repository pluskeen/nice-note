- 在官网复制 linux 版本下载链接
	![[复制node下载链接.png]]
- 使用 `wget` 命令下载 node 的压缩包
	![[wget下载node包.png]]
- 使用 `tar xf` 命令解压包
	![[解压node包.png]]
- 进入解压目录
- 执行 `./bin/node -v` 查看 node 版本
	![[查看node版本.png]]
- 使用 `ln` 命令设置软连接：
	-`ln -s /path/bin/npm /usr/local/bin/`
	-`ln -s /path/bin/node /usr/local/bin/`