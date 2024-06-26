# Linux服务器使用蒲公英进行组网

## 准备工作

安装docker

docker hub无法访问（镜像下载失败）时，如下设置代理镜像（直接复制执行）【注意：可能需要根据参考链接更新镜像列表】
> 参考：https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6?permalink_comment_id=5092155#gistcomment-5092155

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": [
        "https://hub.uuuadc.top",
        "https://docker.anyhub.us.kg",
        "https://dockerhub.jobcher.com",
        "https://dockerhub.icu",
        "https://docker.ckyl.me",
        "https://docker.awsl9527.cn"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 使用docker版本作为Linux客户端

> 参考：https://hub.docker.com/r/bestoray/pgyvpn
> 
> Hint：无权限时适当用sudo提权，以下操作默认提权

```shell
sudo docker pull bestoray/pgyvpn
```

### 预设用户名密码
+ 以下`PGY_USERNAME`为**贝锐账号**或**蒲公英UID**
+ 以下`PGY_PASSWORD`为**蒲公英->管理平台->软件成员->成员列表**中对应密码
```shell
export PGY_USERNAME="xxx"
export PGY_PASSWORD="xxx"
```

### 创建容器
```shell
sudo docker run -d --device=/dev/net/tun --net=host --cap-add=NET_ADMIN --env PGY_USERNAME=$PGY_USERNAME --env PGY_PASSWORD=$PGY_PASSWORD --name pgyvpn bestoray/pgyvpn
```
> 示例（已去敏）：
> ```shell
> export PGY_USERNAME="6468****4:001"
> export PGY_PASSWORD="server1#"
> sudo docker run -d --device=/dev/net/tun --net=host --cap-add=NET_ADMIN --env PGY_USERNAME=$PGY_USERNAME --env PGY_PASSWORD=$PGY_PASSWORD --name pgyvpn bestoray/pgyvpn
> ```

查看docker容器
```shell
sudo docker ps -a
```
> 结果：
> |CONTAINER ID  | IMAGE |            COMMAND      |            CREATED     |    STATUS       |             PORTS                            |                                                       NAMES|
> |-|-|-|-|-|-|-|
> |48f1e7350107 |  bestoray/pgyvpn |  "/usr/share/pgyvpn/s…"  | 4 seconds ago |  Up 4 seconds      |                                 |                                                               pgyvpn|

### 设置断网重连

**进入容器**

```shell
sudo docker exec -it pgyvpn /bin/bash
```
> 结果：
> ```shell
> bash-5.1# pwd
> /root
> ```

```shell
cd /home
cat > login.sh <<'EOF'
while true
do
        stat=$(pgyvisitor logininfo)
        emsg="Please execute the login command first"
        if [[ "$stat" == "$emsg" ]]
        then
                echo "Pgy Offline, retry"
                pgyvisitor login -u $PGY_USERNAME -p $PGY_PASSWORD
        else
                echo "Pgy Online"
        fi
        sleep 30
done
EOF
```

登录局域网

```shell
nohup sh login.sh 2>&1 &
```

`exit`或`ctrl+D`退出容器
