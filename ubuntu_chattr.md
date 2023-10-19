# Ubuntu太老并且在apt时提示chattr没权限

https://blog.csdn.net/Haulyn5/article/details/127495565

下载对应版本e2fsprogs的deb包，解压后使用里面的usr/bin/chattr来设置原本的chattr和lsattr，并将它们移除（备份）
例如，Ubuntu 18.04对应的deb可以在https://launchpad.net/~ubuntu-security-proposed/+archive/ubuntu/ppa/+build/23812170/+files/e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb下载，
寻找方式为：
1. https://launchpad.net/ubuntu/+source/e2fsprogs页面，点开下方的1.44.1-1ubuntu1.4链接
2. 在右侧builds中点amd64
3. 在下方built-files里面找到e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb并获取下载链接（如上）

解压方式为：
```shell
mkdir extracted
dpkg -x e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb extracted
```
然后在extracted目录下可以找到 `usr/bin/chatter` 和 `usr/bin/lsattr`
```shell
cd extracted/usr/bin
./chattr -ia /usr/bin/chattr
./chattr -ia /usr/bin/lsattr
mv /usr/bin/chattr /usr/bin/chattr.bak
mv /usr/bin/lsattr /usr/bin/lsattr.bak
```
