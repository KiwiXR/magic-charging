# wandb local server常见问题

## 在自定义位置启用wandb server的存储

> https://github.com/wandb/server/issues/89#issuecomment-1291412131

```shell
docker run --rm -d -v /home/wandb-local:/vol -p 8080:8080 --name wandb-local wandb/local
```
其中，

`--rm` 表示在docker容器退出（exit）时删除容器，所以在实际部署时**避免使用**！

`-d` (`--detach`) 表示后台运行

`-v` 将数据卷链接到本地

`-p` 表示端口映射

`--name` 为容器名，不能重复

## 首次启动wandb server时报错

> https://github.com/wandb/wandb/issues/2941#issuecomment-974269715

可能的原因是权限不够：

```shell
chgrp -R 0 /home/wandb-local
chmod -R g+rwX /home/wandb-local
```

```text
An error was encountered while starting the container:
2023/11/19 08:54:46 Error adding user: open /vol/env/users.htpasswd: no such file or directory
panic: Can't create default user

goroutine 1 [running]:
main.ensureDefaults({{0x15a7a6f, 0x4}, {0x0, 0x0}, {0xc000046016, 0x16}, {0xc0000441b2, 0xa}, {0x15c4ed4, 0x1a}, ...})
	/mnt/ramdisk/core/services/local/cmd/local/main.go:630 +0xa05
main.commands({{0x15a7a6f, 0x4}, {0x0, 0x0}, {0xc000046016, 0x16}, {0xc0000441b2, 0xa}, {0x15c4ed4, 0x1a}, ...})
	/mnt/ramdisk/core/services/local/cmd/local/main.go:395 +0x1df8
main.main()
	/mnt/ramdisk/core/services/local/cmd/local/main.go:740 +0x78
If this error persists, please contact support@wandb.com and include the message above.
```

## 卡在Loading your local environment...

至少等分钟

## docker容器相关操作

交互式进入容器

```shell
docker exec -it wandb-local /bin/bash
```

退出交互式界面且不关闭容器

```shell
Ctrl+P Ctrl+Q
```

重启容器

```shell
docker restart wandb-local
```
