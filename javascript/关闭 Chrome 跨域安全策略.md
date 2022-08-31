##### 关闭 Chrome 跨域安全策略

1、关闭 Chrome；
2、新建文件夹 `MyChromeDevUserData`，文件夹名自定义，记住文件夹的路径；
3、找到 Chrome 快捷方式，右键 Chrome 图标 --> 属性 --> 目标，修改目标内的内容；

![[Chrome 快捷方式属性.png]]

4、在文件名后面添加以下内容 `--disable-web-security --user-data-dir="path\MyChromeDevUserData"`。
`path` 是第二步文件夹的绝对路径。

修改完后，类似如下内容
`"C:\Program Files\Google\Chrome\Application\chrome.exe" --disable-web-security --user-data-dir="C:\Users\Administrator\Desktop\MyChromeDevUserData"`
