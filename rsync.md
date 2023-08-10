# 使用rsync备份目录
``` shell
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

