# loss-landscape从入坑到入土

> Hao Li, Zheng Xu, Gavin Taylor, Christoph Studer and Tom Goldstein. [Visualizing the Loss Landscape of Neural Nets.](https://arxiv.org/abs/1712.09913) NIPS, 2018.
>
> Keskar, Nitish Shirish, et al. [On large-batch training for deep learning: Generalization gap and sharp minima.](https://arxiv.org/abs/1609.04836) arXiv preprint arXiv:1609.04836 (2016).
> 
> https://github.com/tomgoldstein/loss-landscape
>
> https://jtuckerk.github.io/loss_landscape.html
>
> https://losslandscape.com/

## loss-landscape用途 TL;DR

1. flatness/sharpness: 由该论文（即该仓库）定义的 loss-landscape 越 sharp，（在测试集上）的 generalization 能力越差
   > 见论文 Fig. 3
   > 
   > Using the filter-normalized plots in Figure 3, we can make side-by-side comparisons between minimizers, and we see that now sharpness correlates well with generalization error. Large batches produced visually sharper minima (although not dramatically so) with higher test error.
3. loss-landscape 越具有凸性（Convexity），模型越容易优化
   > 见论文 Fig. 5

[loss-landscape](https://github.com/tomgoldstein/loss-landscape) 库是一个上古可视化工具，但是它还算好（neng）用，所以本文试图把它挖出来考古。

注意：本文对python包版本的要求与原仓库**和而不同**，版本的选择完全遵循个人美学。

## Forks

其他一些看起来promising的forks

https://github.com/voidf/loss-landscape

## Requirements

+ python=3.6
+ torch=1.10.2+cu111
+ openmpi=3.1.2
+ mpi4py=2.0.0
+ numpy=1.15.1 (1.17 tested-ok)
+ h5py=2.7.0
+ matplotlib=2.0.2
+ scipy=0.19.1
+ scikit-learn=0.24.2
+ seaborn=0.9.1
+ pandas=1.0.5

## Setup Steps

### 1. python=3.6

h5py=2.7.0和mpi4py=2.0.0对python版本有要求，因此采用**python3.6**
- h5py=2.7.0 -> python[version='>=2.7,<2.8.0a0|>=3.5,<3.6.0a0|>=3.6,<3.7.0a0']
- mpi4py=2.0.0的 [wheel只提供到py35](https://pypi.org/project/mpi4py/2.0.0/#files)，但是py36也可以从源码编译（pip完成）

```shell
conda create -p /path/conda/envs/landscape -y python=3.6
conda activate /path/conda/envs/landscape
```

### 2. torch=1.10.2+cu111

安装torch=1.10.2+cu111，原因见 [1.10.X should be the last official PyTorch release to support Python 3.6](https://github.com/pytorch/pytorch/issues/66462)

```shell
pip install torch==1.10.2+cu111 torchvision==0.11.3+cu111 torchaudio==0.10.2 -f https://download.pytorch.org/whl/cu111/torch_stable.html
```

### 3. openmpi=3.1.2

> https://github.com/tomgoldstein/loss-landscape/issues/8#issuecomment-466772651
> 
> https://mpi4py.readthedocs.io/en/stable/appendix.html#building-mpi

下载并解压安装openmpi=3.1.2

```shell
cd /path/to/download
wget https://download.open-mpi.org/release/open-mpi/v3.1/openmpi-3.1.2.tar.gz
mkdir ~/openmpi
cp openmpi-3.1.2.tar.gz ~/openmpi
tar -zxvf openmpi-3.1.2.tar.gz
cd openmpi-3.1.2/
./configure --prefix=$HOME/openmpi
make -j8 all
make install
vim ~/.bashrc
```

在 `.bashrc` 中加入如下行

```shell
# in vim
export PATH=$HOME/openmpi/bin:$PATH
export LD_LIBRARY_PATH=$HOME/openmpi/lib:$LD_LIBRARY_PATH
```

退出 `vim`，执行使之生效

```shell
source ~/.bashrc
```

确保安装成功

```shell
mpiexec --version
mpirun --version
```

### 4. 其余依赖

scikit-learn=0.24.2中包含了scipy=0.19.1，[参考](https://pypi.org/project/scikit-learn/0.24.2/)

```shell
pip install mpi4py==2.0.0 numpy==1.15.1 h5py==2.7.0 matplotlib==2.0.2 scikit-learn==0.24.2 scipy==0.19.1 seaborn==0.9.1 pandas==1.0.5
```

## Replicate Examples

本节复现 [原仓库](https://github.com/tomgoldstein/loss-landscape) 的结果

```shell
git clone git@github.com:tomgoldstein/loss-landscape.git
```



从作者提供的google网盘下载 [VGG-9](https://drive.google.com/open?id=1jikD79HGbp6mN1qSGojsXOZEM5VAq3tH) 和 [ResNet-56](https://drive.google.com/a/cs.umd.edu/file/d/12oxkvfaKcPyyHiOevVNTBzaQ1zAFlNPX/view?usp=sharing) 的tar包，放置于 `cifar10/trained_nets` 路径并解压

```shell
tar -zxvf vgg9.tar.gz
tar -zxvf resnet56.tar.gz
```

### Visualizing 1D loss curve

#### Creating 1D linear interpolations

该实验可以直接复现，故省略过程。需要注意的是，结果文件输出在 `model_file` 和 `model_file2` 的最近公共目录（类比最近公共祖先），存在 `model_file3` 时则是与前述输出路径比较。

#### Producing plots along random normalized directions

由于下载的文件中已经生成了h5文件，所以直接执行部分实验时会被跳过，为了完整复现实验，可以重新建立实验目录

```shell
mkdir -p cifar10/trained_nets/tmp/
cd cifar10/trained_nets/tmp/
cp ../vgg9_sgd_lr=0.1_bs=128_wd=0.0_save_epoch=1/model_300.t7 ./
# reproduce
mpirun -n 4 python plot_surface.py --mpi --cuda --model vgg9 --x=-1:1:51 \
--model_file cifar10/trained_nets/tmp/model_300.t7 \
--dir_type weights --xnorm filter --xignore biasbn --plot
```

运行完毕后，在目录 `cifar10/trained_nets/tmp/` 下会多出5个文件，两个h5文件和三个pdf文件。其中
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter.h5` 是direction文件，表示模型参数扰动的方向
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter.h5_[-1.0,1.0,51].h5` 是surface文件，表示模型扰动后形成的loss landscape，可视化用的就是该文件
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter.h5_[-1.0,1.0,51].h5_1d_loss_acc.pdf` 是可视化文件，同时可视化了loss和accuracy（train还是split由`crunch`函数调用时决定），横轴为参数扰动的程度（direction文件的方向为正方向）
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter.h5_[-1.0,1.0,51].h5_1d_train_err.pdf` 是可视化文件，可视化了train err
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter.h5_[-1.0,1.0,51].h5_1d_train_loss.pdf` 是可视化文件，可视化了train loss

### Visualizing 2D loss contours

同样可以与上述类似地提取模型文件到新目录进行生成，此处为节省时间直接生成

```shell
mpirun -n 4 python plot_surface.py --mpi --cuda --model resnet56 --x=-1:1:51 --y=-1:1:51 \
--model_file cifar10/trained_nets/resnet56_sgd_lr=0.1_bs=128_wd=0.0005/model_300.t7 \
--dir_type weights --xnorm filter --xignore biasbn --ynorm filter --yignore biasbn --plot
```

生成的文件中有两个h5文件、四个pdf文件
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter_yignore=biasbn_ynorm=filter.h5` 是direction文件
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter_yignore=biasbn_ynorm=filter.h5_[-1.0,1.0,51]x[-1.0,1.0,51].h5` 是surface文件
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter_yignore=biasbn_ynorm=filter.h5_[-1.0,1.0,51]x[-1.0,1.0,51].h5_train_loss_2dcontour.pdf` 以2D等高线方式可视化
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter_yignore=biasbn_ynorm=filter.h5_[-1.0,1.0,51]x[-1.0,1.0,51].h5_train_loss_2dcontourf.pdf` 以2D填充的等高线方式可视化
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter_yignore=biasbn_ynorm=filter.h5_[-1.0,1.0,51]x[-1.0,1.0,51].h5_train_loss_2dheat.pdf` 以热力图方式可视化
+ `model_300.t7_weights_xignore=biasbn_xnorm=filter_yignore=biasbn_ynorm=filter.h5_[-1.0,1.0,51]x[-1.0,1.0,51].h5_train_loss_3dsurface.pdf` 使用matplotlib实现简单的3D可视化

得到surface的h5文件后，可以进一步控制可视化结果，详见原仓库

```shell
python plot_2D.py --surf_file cifar10/trained_nets/resnet56_sgd_lr=0.1_bs=128_wd=0.0005/model_300.t7_weights_xignore=biasbn_xnorm=filter_yignore=biasbn_ynorm=filter.h5_[-1.0,1.0,51]x[-1.0,1.0,51].h5 --surf_name train_loss
```

### Visualizing 3D loss surface

得到的h5文件还可以转为vtp文件格式，这一格式可以用 [ParaView](http://paraview.org/) 查看、**旋转**和保存图像，详见原仓库

```shell
python h52vtp.py --surf_file path_to_surf_file --surf_name train_loss --zmax 10 --log
```
