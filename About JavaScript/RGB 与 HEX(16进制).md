#### RGB
RGB色彩的标记方式是 RGB(0~255, 0~255, 0~255)，代表(红, 绿, 蓝)的色彩数值。
RGB(0, 0, 0) 也就是红，绿，蓝都没有，就是黑色。
RGB(255, 255, 255)是红，绿，蓝都最大值就成了白色。
RGB(255, 0, 0)就是红色。
可以这样记忆：数值越小越黑暗。

#### HEX
表示形式为`#`和6位16进制的数值，每两位数字一次代表红、绿、蓝，即`#RRGGBB`。每位数字取值范围是 0-F 。10对应A，11对应B，12对应C，13对应D，14对应E，15对应F。

#### RGB 与 HEX 的转换
##### RGB 转 HEX
HEX的一个三原色数值等于RGB的三原色数值除以16，第一位是商，第二位是余数。
```
RGB(169, 245, 8)
R: 169 / 16 = 10 余 9，而10对应A，即 A9
G: 245 / 16 = 15 余 5, 而15对应F，即 F5
B: 8 / 16 = 0 余 8，即 08
所以RGB(169, 245, 8) = #A9F508
```

##### HEX 转 RGB
RGB的数值是16乘以HEX的第一位加上HEX的第二位，数字10以下的RGB和HEX都是相同的，但需要在前面补0成两位数。
```
#A9F508 = RGB(16 * 10 + 9， 16 * 15 + 5, 16 * 0 + 8) = RGB(169, 245, 8)
```

#### Javascript 实现转换
##### RGB 转 HEX
```javascript
/**  
 * RGB 转 HEX  
 * @param rgb 参数格式 rgb(x, y, z)，如：rgb(123, 233, 092)  
 */
rgbToHex(rgb) {  
	const color = rgb.toString().match(/\d+/g); // 把 x,y,z 推送到 color 数组里  
	let hex = '#';  

	for (let i = 0; i < 3; i++) {  
		// 'Number.toString(16)' 是JS默认能实现转换成16进制数的方法.  
		// 'color[i]' 是数组，要转换成字符串.  
		// 如果结果是一位数，就在前面补零。例如： A变成0A  
		hex += ('0' + Number(color[i]).toString(16)).slice(-2);  
	}  
	return hex;  
}
```

##### HEX 转 RGB
```javascript
/**  
 * HEX 转 RGB  
 * @param hex 参数格式 #e96f02
 */
hexToRgb(hex) {  
	const rgb = [];  

	hex = hex.substr(1); // 去除前缀 # 号  

	// 处理 "#abc" 成 "#aabbcc"
	if (hex.length === 3) {
		hex = hex.replace(/(.)/g, '$1$1');  
	}  
	// 按16进制将字符串转换为数字  
	hex.replace(/../g, (color) => rgb.push(parseInt(color, 0x10)));  

	return 'rgb(' + rgb.join(', ') + ')';  
}
```
