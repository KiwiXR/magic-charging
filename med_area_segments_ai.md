# 使用segments.ai替代面积法

## 预处理

预处理图片为png格式，最好为单通道（灰度图）

## 标注和发布

> https://segments.ai

1. 在segments.ai上创建数据集（选择Medical，Image，segmentation-bitmap）
2. 将预处理完的图片上传到segments.ai
3. 使用segments.ai创建标签并保存，注意标签id默认为1（此时状态应该为Labeled）
4. 在Releases菜单栏下发布数据集（此处假定版本号为v0.0）

## 导出数据集

### 配置python环境

命令行
```shell
pip install segments-ai
```

### 下载数据集

> https://docs.segments.ai/how-to-integrate/export/exporting-the-release-file-to-different-formats

以下为python代码

download.py
```python
from segments import SegmentsClient, SegmentsDataset
from segments.utils import export_dataset

client = SegmentsClient('api_key')  # 此处替换为自己的API Key，位于Settings->API Keys
release = client.get_release('username/datasetname', 'v0.0')  # 此处替换为自己的username，dataset name，版本号
dataset = SegmentsDataset(release, labelset='ground-truth', filter_by=['labeled'])

export_dataset(dataset, export_format='semantic')
```

命令行
```shell
python download.py
```

## 统计面积

以下python代码统计每张标签中的总像素数、标注像素数并与原图片文件名一同保存到csv文件中

solve.py
```python
import os
import argparse
from PIL import Image
import numpy as np
import csv


def findAllFile(base, suffix):
    for root, ds, fs in os.walk(base):
        for f in fs:
            if f.endswith(suffix):
                fullname = os.path.join(root, f)
                yield fullname


def main():
    parser = argparse.ArgumentParser('A Simple Parser')
    parser.add_argument('root', type=str, help='path to dataset')
    parser.add_argument('-s', '--suffix', type=str, help='suffix of label files', default='_label_ground-truth_semantic.png')
    parser.add_argument('-o', '--output', type=str, help='output file path', default='result.csv')
    args = parser.parse_args()
    
    results = []
    for path in findAllFile(args.root, args.suffix):
        img = Image.open(path)
        img = np.asarray(img)
        tot_pix = np.prod(img.shape)
        lbl_pix = np.sum(img)
        # assemble results dict
        result = dict(image_name=os.path.basename(path).replace(args.suffix, '.png'), labeled_area=lbl_pix, total_area=tot_pix)
        results.append(result)
    
    with open(args.output, 'w', newline='') as csvfile:
        fieldnames = ['image_name', 'labeled_area', 'total_area']
        writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
        writer.writeheader()
        writer.writerows(results)


if __name__ == '__main__':
    main()

```

指定数据集路径，标签后缀（默认）和输出csv文件名或路径（默认）

命令行
```shell
python solve.py segments/username_datasetname/v0.0/
```

csv文件内容范例为：

| image_name | labeled_area | total_area |
| ---- | ---- | ---- |
| a.png | 809722 | 4000000 |
| b.png | 1117284 | 4000000 |
| c.png | 734683 | 4000000 |


DONE！