# 使用rsync备份目录
``` bash
rsync -avzP --copy-unsafe-links /src/path /tgt/path
```
-a: --archive (-rlptgoD) 
>-r: --recursive \
>-l: --links 拷贝软链接（已包含在-a中）\
>-p: --perms 保留权限 \
>-t: --times 保留修改时间 \
>-g: --group 保留组（非超级用户调用则只保留调用者所在组） \
>-o: --owner 保留拥有者（超级用户） \
>-D: --devices --specials 保留设备文件（超级用户） + 保留特殊文件

-v: --verbose \
-z: --compress \
-P: --partial --progress 断点续传 + 进度条 \
--copy-unsafe-links: 当软链接指向文件在同步范围外时拷贝文件，否则拷贝链接

```bash
rsync -avzP --exclude '*foo.jpg' --exclude 'bar/*' /src/path /tgt/path
```
```bash
rsync -avzP --exclude={'*foo.jpg','bar/*'} /src/path /tgt/path
```

## --exclude, --exclude-from, --include, --include-from, --files-from
[[exclude]](https://bobcares.com/blog/rsync-exclude/) 
[[files-from]](https://stackoverflow.com/questions/16647476/how-to-rsync-only-a-specific-list-of-files)
[[include exclude patterns]](https://stackoverflow.com/a/48010623)

注意：exclude或include指定的路径必须是**相对路径**，相对于/src/path

For more details:
```bash
$ man rsync
```
search for the rules in man:
```vim
/INCLUDE/EXCLUDE PATTERN RULES
```


``` bash
$ rsync -V
rsync  version 3.2.7  protocol version 31
Copyright (C) 1996-2022 by Andrew Tridgell, Wayne Davison, and others.
...
```
