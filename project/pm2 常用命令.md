##### 启动

```shell
# 启动应用
pm2 start app.js
```

启动时有些选项可使用，更多的选项参考官方文档：

```shell
# 指定应用的名称
--name <app_name>

# 监听应用目录变化，一旦发生变化，自动重启
--watch

# 排除监听的应用目录
--ignore-watch="node_modules test"

# 设置应用重启的内存阈值
--max-memory-restart <200MB>

# 指定输出日志文件路径
--output <path>

# 指定错误日志文件路径
--error <path>
```


##### 管理进程

```shell
# 重启应用
pm2 restart <app_name>

# 0秒停机重载应用
pm2 reload <app_name>

# 停止应用
pm2 stop <app_name>

# 删除应用
pm2 delete <app_name>
```

上面的 `<app_name>` 可以替换为：

- `<app_id>` 作用于指定进程
- `all` 对于所有进程生效


##### 查看与监视进程

```shell
# 显示所有进程信息
pm2 list

# 查看进程 id 的详细信息
pm2 info <app_id>
pm2 show <app_id>

# 监视每个进程的实时情况
pm2 monit
```


##### 日志操作

```shell
# 显示所有进程的日志
pm2 logs

# 显示 <app_id> 进程的日志
pm2 logs <app_id>

# 清空所有日志文件
pm2 flush

# 重载所有日志
pm2 reloadLogs
```


##### pm2 更新

```shell
pm2 update
```


##### 开机自动启动

1、先启动应用；
2、通过 `pm2 save` 保存当前已启动的应用状态；
3、通过 `pm2 startup` 开启开机自启动服务。

可使用 `pm2 unstartup` 删除当前自启动的配置。

更新 node 版本后，自启动服务需要重新设置，先使用 `pm2 unstartup` 删除，后执行上述步骤。

如果不需要自启动服务，但要恢复已保存的应用状态，可以使用 `pm2 resurrect` 命令。