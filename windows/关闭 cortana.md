##### 组策略禁用 Cortana
适合专业版用户。win + R 打开运行，输入 gpedit.msc 回车打开组策略。

左边栏选择 计算机配置 -> 管理模版 -> Windows组件 -> 搜索，双击 “允许使用 Cortana” -> 已禁用 -> 确定。
![[gpedit.msc disabled cortana.jpg]]


##### 注册表关闭 Cortana
适合家庭版用户。win + R 打开运行，输入 regedit 回车打开注册表。

地址栏定位到：`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Windows Search`

右键 Windows Search 新建 **DWORD (32位) 值**，并命名为 **AllowCortana**，然后双击 AllowCortana，将 “数值数据” 设置为 **0**，确定。
![[regedit close cortana.jpg]]