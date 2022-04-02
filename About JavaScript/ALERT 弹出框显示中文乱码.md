![[alert乱码.png]]

有两种可能：
1. JavaScript与HTML文件编码方式不一样
	在引入JavaScript的代码中指定其字符集为gb2312，代码如下：
	```html
	<script charset="gb2312" type="text/javascript" language="javascript" src=""></script>
	```
	
2. HTML的编码方式不支持显示中文
	在`head`中添加一个`meta`标签，指定HTML编码格式为UTF-8，代码如下：
	```html
	<meta http-equiv="content-type" content="text/html; charset=UTF-8">
	```
