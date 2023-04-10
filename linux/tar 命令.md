
```shell
# 为当前工作目录中的文件夹 /dir/tecmint 创建 tar 归档文件 tecmint-14-09.tar
tar -cvf tecmint-14-09.tar /dir/tecmint/
```

上面命令中的每个选项。

-  c – 创建一个新的 **.tar** 归档文件。
-  v – 详细显示 **.tar** 文件进度。
-  f – 存档文件的文件名类型。



要解压或提取 tar 文件，只需使用选项 `x` 发出以下命令。

想解压非当前目录中文件，使用选项 `-C` 指定目录。

```shell
# 解压当前目录中的文件
tar -xvf public_html.tar

# 解压指定目录中的文件，-C 指定目录
tar -xvf public_html.tar -C /home/public_html/videos/
```



要列出 tar 存档文件的内容，只需运行以下带选项 `t` 的命令。

```shell
# 不解压的情况下列出压缩包的内容
tar -tvf uploadprogress.tar
```



提取某个文件

```shell
# 从 .tar 中提取文件
tar -xvf cleanfiles.sh.tar cleanfiles.sh

# 从 .tar.gz 中提取文件
tar -zxvf tecmintbackup.tar.gz tecmintbackup.xml

# 从 .tar.bz2 中提取文件
tar -jxvf Phpfiles-org.tar.bz2 home/php/index.php
```



提取多个文件

```shell
tar -xvf tecmint-14-09-12.tar 'file1' 'file2'

tar -zxvf MyImages-14-09-12.tar.gz 'file1' 'file2'

tar -jxvf Phpfiles-org.tar. bz2 'file1' 'file2'
```



使用通配符提取一组文件

```shell
# 文件名匹配选项，--wildcards 使用通配符

tar -xvf Phpfiles-org.tar --wildcards '*.php'

tar -zxvf Phpfiles-org.tar.gz --wildcards '*.php'

tar -jxvf Phpfiles-org.tar.bz2 --wildcards '*.php'
```



要将文件或目录添加到现有的 tar 存档文件，使用选项 `r` 追加到末尾。

```shell
# 将 xyz.txt 文件添加到 tecmint-14.tar 末尾
tar -rvf tecmint-14.tar xyz.txt

# 将 php 目录添加到 tecmint-14.tar 末尾
tar -rvf tecmint-14.tar php
```



创建 tar.gz 文件时排除某些文件和目录，使用 `--exclude` 选项。

```shell
# 从 /home/tecmint 文件夹中排除 file1.txt 文件
tar --exclude='file1.txt' -zcvf backup.tar.gz /home/tecmint 

# 从 /home/tecmint 文件夹中排除 uploads 目录
tar --exclude='/home/tecmint/uploads' -zcvf backup.tar.gz /home/tecmint

# 排除特定扩展名文件，排除了所有 .txt 文件
tar --exclude='*.txt' -zcvf backup.tar.gz /home/tecmint
```



使用 `--delete` 选项从已创建的 tar 文件中删除文件或目录。
```shell
tar --delete -f backup.tar.gz file1.txt 

tar --delete -f backup.tar.gz '/home/tecmint/uploads'
```


拓展内容

-   `-c` – 创建存档文件。
-   `-x` – 提取存档文件。
-   `-v` – 显示存档文件的进度。
-   `-f` – 存档文件的文件名。
-   `-t` – 查看存档文件的内容。
-   `-u` – 存档并添加到现有存档文件。
-   `-j` – 通过 bzip2 过滤存档。
-   `-z` – 通过 gzip 过滤存档。
-   `-r` – 将文件或目录附加或更新到现有存档文件。
-   `-W` – 验证存档文件。
-   `-A` – 连接存档文件。
-   `--wildcards` – 指定文件名匹配模式。
-   `--exclude` – 创建存档时排除文件和目录。
-   `--delete` – 从存档中删除文件和目录。