# Ubuntu太老并且在apt时提示chattr没权限

https://blog.csdn.net/Haulyn5/article/details/127495565

下载对应版本e2fsprogs的deb包，解压后使用里面的usr/bin/chattr来设置原本的chattr和lsattr，并将它们移除（备份）

```shell
./chattr -ia /usr/bin/chattr
./chattr -ia /usr/bin/lsattr
mv /usr/bin/chattr /usr/bin/chattr.bak
mv /usr/bin/lsattr /usr/bin/lsattr.bak
```
