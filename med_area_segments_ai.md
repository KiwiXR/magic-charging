# 使用segments.ai替代面积法

## 环境配置

**强烈建议使用conda配置环境**

### 创建环境（只要做一次）

命令行
```shell
conda create -n med -y python
```
这里`-n`后面的`med`是环境的名字，如果环境名另起，则在下面把`med`全部替换为新的名称

### 激活环境（_每次使用下面任意代码之前，每次使用下面任意代码之前，每次使用下面任意代码之前_）

激活环境（每次）

命令行
```shell
conda activate med
```

> 注：需要查看当前所有conda环境时，使用
命令行
```shell
conda env list
```

安装需要的包（只要做一次）

命令行
```shell
pip install tqdm segments-ai Pillow numpy scikit-image
```

## 预处理

**需要先激活环境！**

预处理图片为png格式，最好为单通道（灰度图）

以下python代码功能为，将tiff格式的单通道图片转换为png格式：

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

以下代码实际执行转换：

命令行
```shell
python convert.py -s /path/to/tiff/images/ -t /path/to/png/images/
```

注：此处的`/path/to/tiff/images/`和`/path/to/png/images/`都是占位符！

实际使用时（Windows下，会用Linux就不用往下看了），例如图片文件夹在`D:\tiff图片\`，而希望保存到`D:\png图片\`，此时**不需要**创建`D:\png图片\`文件夹。

实际执行的命令为（如果路径中含有空格，则使用**英文双引号**将路径包含，例如`"D:\tiff 图片"`）：

命令行
```shell
python convert.py -s D:\tiff图片\ -t D:\png图片\
```

此时，转换完成的png图片将会保存到`D:\png图片\`文件夹下。

也可以使用相对路径，即相对于代码执行位置的路径。

例如假设当前在E盘下`E:\`，执行：

命令行
```shell
python convert.py -s D:\tiff图片\ -t png图片
```
执行完成后，转换完成的png图片将会保存到`E:\png图片\`文件夹下。

## 标注和发布

> https://segments.ai

1. 在segments.ai上创建数据集（选择Medical，Image，segmentation-bitmap）
2. 将预处理完的图片上传到segments.ai
3. 使用segments.ai创建标签并保存，注意标签id默认为1（此时状态应该为Labeled）
4. 在Releases菜单栏下发布数据集（此处假定版本号为v0.0）

## 导出数据集

**需要先激活环境！**

### 下载数据集

> https://docs.segments.ai/how-to-integrate/export/exporting-the-release-file-to-different-formats

以下为python代码，在使用前根据注释修改**所有需要的内容**！

download.py
```python
from segments import SegmentsClient, SegmentsDataset
from segments.utils import export_dataset

client = SegmentsClient('api_key')  # 此处替换为自己的API Key，位于Settings->API Keys
release = client.get_release('username/datasetname', 'v0.0')  # 此处替换为自己的username，dataset name，版本号
dataset = SegmentsDataset(release, labelset='ground-truth', filter_by=['labeled','reviewed'])

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
