# Ubuntu太老并且在apt时提示dpkg backup没权限

## 报错关键信息

```shell
dpkg: error processing archive /var/cache/apt/archives/e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb (--unpack):
 unable to make backup link of './usr/bin/chattr' before installing new version: Operation not permitted
```

```shell
dpkg: error processing archive /var/cache/apt/archives/openssh-server_1%3a7.6p1-4ubuntu0.7_amd64.deb (--unpack):
 unable to make backup link of './usr/sbin/sshd' before installing new version: Operation not permitted
```
这可能是因为系统自带的chattr坏了！

## 解决方案

### 思路
下载对应版本 e2fsprogs 的 deb 包，解压后使用里面的 usr/bin/chattr 来设置原本的 chattr 和 lsattr，并将它们移除（备份）以避免错误发生

### 操作
例如，Ubuntu 18.04 对应的 deb 可以在 https://launchpad.net/~ubuntu-security-proposed/+archive/ubuntu/ppa/+build/23812170/+files/e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb 下载，
寻找方式为：
1. 在 https://launchpad.net/ubuntu/ 进行搜索 e2fsprogs
2. 点开搜索结果，进入 https://launchpad.net/ubuntu/+source/e2fsprogs 页面
3. 点开页面下方的 1.44.1-1ubuntu1.4 链接
4. 在右侧 builds 中点 amd64
5. 在下方 built-files 里面找到 e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb 并获取下载链接（如上）

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

> 参考：https://blog.csdn.net/Haulyn5/article/details/127495565

## 报错详细内容示例
```shell
sudo apt --fix-broken install
Reading package lists... Done
Building dependency tree
Reading state information... Done
Correcting dependencies... Done
The following packages were automatically installed and are no longer required:
  libxatracker2 libxvmc1 linux-hwe-5.4-headers-5.4.0-100 linux-hwe-5.4-headers-5.4.0-104 linux-hwe-5.4-headers-5.4.0-107 linux-hwe-5.4-headers-5.4.0-109 linux-hwe-5.4-headers-5.4.0-87
  linux-hwe-5.4-headers-5.4.0-89 linux-hwe-5.4-headers-5.4.0-90 linux-hwe-5.4-headers-5.4.0-91 linux-hwe-5.4-headers-5.4.0-92 linux-hwe-5.4-headers-5.4.0-94 linux-hwe-5.4-headers-5.4.0-96
  linux-hwe-5.4-headers-5.4.0-97 linux-hwe-5.4-headers-5.4.0-99 x11-apps x11-session-utils xbitmaps xinit xinput
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  e2fsprogs
Suggested packages:
  gpart fuse2fs e2fsck-static
Recommended packages:
  e2fsprogs-l10n
The following packages will be upgraded:
  e2fsprogs
1 upgraded, 0 newly installed, 0 to remove and 386 not upgraded.
Need to get 0 B/390 kB of archives.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n]
(Reading database ... 482965 files and directories currently installed.)
Preparing to unpack .../e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb ...
Unpacking e2fsprogs (1.44.1-1ubuntu1.4) over (1.44.1-1ubuntu1.3) ...
dpkg: error processing archive /var/cache/apt/archives/e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb (--unpack):
 unable to make backup link of './usr/bin/chattr' before installing new version: Operation not permitted
dpkg-deb: error: paste subprocess was killed by signal (Broken pipe)
update-initramfs: deferring update (trigger activated)
Errors were encountered while processing:
 /var/cache/apt/archives/e2fsprogs_1.44.1-1ubuntu1.4_amd64.deb
E: Sub-process /usr/bin/dpkg returned an error code (1)
```
