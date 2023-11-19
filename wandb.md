```shell
docker run --rm -d -v /home/wandb-local:/vol -p 8080:8080 --name wandb-local wandb/local
```
其中，

`--rm` 表示在docker容器退出（exit）时删除容器，所以在实际部署时**避免使用**！

`-d` (`--detach`) 表示后台运行

`-v` 将数据卷链接到本地

`-p` 表示端口映射

`--name` 为容器名，不能重复
