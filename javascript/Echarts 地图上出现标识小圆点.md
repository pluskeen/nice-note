在 `series.type: 'map'` 时，如果启用 `legend` 图例组件，地图上会出现标识点。

![[地图上有小圆点.png]]


如果需要隐藏，有 2 种方法可以实现：

- 设置 `series.itemStyle.color: 'transparent'`
  ![[图例标识和小圆点都隐藏了.png]]
  
- 设置 `series.showLegendSymbol: false`
  ![[小圆点隐藏了.png]]


