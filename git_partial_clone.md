# git clone部分文件

`git version > 2.37`

```shell
git sparse-checkout set --no-cone "/*" "!/folder/" "!/path/to/dist/*.map"
```

其中：
+ `--no-cone` 表示后面的是正则表达式
+ `"/*"` 表示包含所有内容
+ `"!/folder/"` 表示排除根目录下 `folder` 文件夹
+ `"!/path/to/dist/*.map"` 表示排除目录 `/path/to/dist/` 下后缀为 `.map` 的文件

更新git-for-windows：

```shell
git update-git-for-windows
```
