ssh的配置文件路径 `/etc/ssh/sshd_config`

修改`ssh_config`文件中的 **port**

重启ssh服务 `systemctl restart sshd.service`

如果没有`systemctl`的话可以执行：`/etc/init.d/sshd restart` 或 `/etc/init.d/ssh restart`