# 使用segments.ai替代面积法

## 环境配置

**强烈建议使用conda配置环境**

#### 创建环境

命令行
```shell
conda create -n med -y python
```
这里`-n`后面的`med`是环境的名字，如果环境名另起，则在下面把`med`全部替换为新的名称

#### 激活环境

命令行
```shell
conda activate med
```

注：需要查看当前所有conda环境时，使用
命令行
```shell
conda env list
```

命令行
```shell
pip install tqdm segments-ai Pillow numpy scikit-image
```

## 预处理

预处理图片为png格式，最好为单通道（灰度图）

convert.py
```python
import argparse
import os
from PIL import Image
from tqdm import tqdm

def convert_tiff_to_png(input_folder, output_folder):
    # Create the output folder if it doesn't exist
    os.makedirs(output_folder, exist_ok=True)

    # Get a list of TIFF files in the input folder
    tiff_files = [f for f in os.listdir(input_folder) if f.endswith('.tiff') or f.endswith('.tif')]

    for tiff_file in tqdm(tiff_files):
        # Construct the input and output file paths
        input_path = os.path.join(input_folder, tiff_file)
        output_path = os.path.join(output_folder, os.path.splitext(tiff_file)[0] + '.png')

        # Open the TIFF image using Pillow
        tiff_image = Image.open(input_path)

        # Save the image as PNG
        tiff_image.save(output_path, format='PNG')

        #print(f"Converted {tiff_file} to {os.path.basename(output_path)}")

if __name__ == "__main__":
    # Specify the input and output folders
    parser = argparse.ArgumentParser('A Simple Parser')
    parser.add_argument('-s', '--source', type=str, required=True, help='path to tiff images')
    parser.add_argument('-t', '--target', type=str, required=True, help='path to png images')
    args = parser.parse_args()

    # Convert TIFF to PNG
    convert_tiff_to_png(args.source, args.target)
```

命令行
```shell
python convert.py -s /path/to/tiff/images/ -t /path/to/png/images/
```

## 标注和发布

> https://segments.ai

1. 在segments.ai上创建数据集（选择Medical，Image，segmentation-bitmap）
2. 将预处理完的图片上传到segments.ai
3. 使用segments.ai创建标签并保存，注意标签id默认为1（此时状态应该为Labeled）
4. 在Releases菜单栏下发布数据集（此处假定版本号为v0.0）

## 导出数据集

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
from tqdm import tqdm


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
    for path in tqdm(findAllFile(args.root, args.suffix)):
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
