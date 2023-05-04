`cp [options] 源目录 目标目录`

`option` 参数说明:

-   -a：此选项通常在复制目录时使用，它保留链接、文件属性，并复制目录下的所有内容。其作用等于 `dpr` 参数组合。
-   -d：复制时保留链接。这里所说的链接相当于 Windows 系统中的快捷方式。
-   -f：覆盖已经存在的目标文件而不给出提示。
-   -i：与 -f 选项相反，在覆盖目标文件之前给出提示，要求用户确认是否覆盖，回答 y 时目标文件将被覆盖。
-   -p：除复制文件的内容外，还把修改时间和访问权限也复制到新文件中。
-   -r：若给出的源文件是一个目录文件，此时将复制该目录下所有的子目录和文件。
-   -l：不复制文件，只是生成链接文件。


```shell
# 将 packageA 下的所有文件拷贝到 packageB 中，packageA 文件夹不在 packageB 中
cp -r /home/packageA/* /home/cp/packageB/
cp -r /home/packageA/* /home/cp/packageB
```


```shell
# 将 packageA 文件会拷贝到 packageB 中，packageB 文件夹下有 packageA 文件夹
cp -r /home/packageA /home/packageB
cp -r /home/packageA /home/packageB/
cp -r /home/packageA/ /home/packageB
cp -r /home/packageA/ /home/packageB/
```