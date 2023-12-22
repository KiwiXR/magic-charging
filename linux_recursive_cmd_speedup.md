# 对递归执行的命令如 `chmod -R` 使用 `xargs` 进行加速

## 以 `chown -R` 为例

加速前：
```shell
chown -R usr:grp .
```

加速后：
```shell
find . -type f -print0 | sudo xargs -0 --max-args=1 --max-procs=50 chown usr:grp -R
```
