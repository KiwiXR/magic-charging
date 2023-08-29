# 调整数据集（层级目录）结构

`restruct_acdc.sh` 须放在项目根目录下，与 `data` 目录同级文件内容：
``` bash
#!/bin/bash

restruct(){
	find $1 |
		while read oldpath; do
			echo $oldpath
			oldstr=$(echo $oldpath | awk -F '/' '{printf "%s/%s",$4,$5}')
			echo $oldstr
			newstr=$(echo $oldpath | awk -F '/' '{printf "%s/%s",$5,$4}')
			echo $newstr
			newpath=$(echo $oldpath | sed "s@$oldstr@$newstr@")
			echo $newpath

			mkdir -p "$(dirname "$newpath")"
			echo $(dirname "$newpath")
			rsync -a "$oldpath" "$newpath"
		done
}

[[ "${TEST}" == "1" ]] && \
restruct data/acdc/rgb_anon/*/train/*/* && \
exit 0

restruct "data/acdc/rgb_anon/*/train/*/*" > /dev/null
restruct "data/acdc/rgb_anon/*/val/*/*" > /dev/null
restruct "data/acdc/gt/*/train/*/*_labelTrainIds.png" > /dev/null
restruct "data/acdc/gt/*/val/*/*_labelTrainIds.png" > /dev/null
```

实际使用时，首先赋予运行权限：
``` bash
chmod +x restruct_acdc.sh
```
接着运行或测试：
``` bash
./restruct_acdc.sh
```
``` bash
$ TEST=1 ./restruct_acdc.sh
data/acdc/rgb_anon/fog/train/GOPR0475/GOPR0475_frame_000041_rgb_anon.png
fog/train
train/fog
data/acdc/rgb_anon/train/fog/GOPR0475/GOPR0475_frame_000041_rgb_anon.png
data/acdc/rgb_anon/train/fog/GOPR0475
```
