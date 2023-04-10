find命令用于在指定目录下查找文件。
语法：`find + 目标目录(路径) + <选项> + <选项参数>`

```shell
# 查找当前目录及其子目录下所有文件和文件夹
find .
```

```shell
# -name <string> 根据指定字符串查找文件名和目录名
# 在 /temp/log 目录下查找 .txt 结尾的文件名
# 需要书写完整的路径
find /temp/log -name "*.txt"
```

```shell
# 组合查找文件名以`file1`开头（与、或、非）`file2`开头的文件
# -a 与（取交集）
# -o 或（取并集）
# -not 非（同 !）
# ! 非（同 not）
find . -name "file1*" -a -name "file2*"
find . -name "file1*" -o -name "file2*"
find . -name "file1*" -not -name "file2*"
find . -name "file1*" ! -name "file2*"
```

```shell
# -type <文件类型> 只寻找符合指定的文件类型的文件
find . -type f
```

| 文件类型参数 | 含义 |
| ---------- | -- |
| f | 普通文件 |
| d | 目录 |
| l | 符号连接 |
| c | 字符设备 |
| b | 块设备 |
| s | 套接字 |

```shell
# -maxdepth <目录层级> 设置最大目录层级
# -mindepth <目录层级> 设置最小目录层级
find . -maxdepth 3 -type f
```

```shell
# -perm <权限数值> 查找符合指定的权限数值的文件或目录
# 搜索权限为 777 的文件
find . -type f -perm 777

# 搜索 .txt 格式且权限不为 777 的文件
find . -type f -name "*.txt" ! -perm 777
```
