# loss-landscape从入坑到入土

> Visualizing the Loss Landscape of Neural Nets
> 
> https://github.com/tomgoldstein/loss-landscape

[loss-landscape](https://github.com/tomgoldstein/loss-landscape)库是一个上古可视化工具，但是它还算好（neng）用，所以本文试图把它挖出来考古。

注意：本文对python包版本的要求与原仓库**和而不同**，版本的选择完全遵循个人美学。

## Requirements

+ python=3.6
+ torch=1.10.2+cu111
+ openmpi=3.1.2
+ mpi4py=2.0.0
+ numpy=1.15.1
+ h5py=2.7.0
+ matplotlib=2.0.2
+ scipy=0.19



## Steps

h5py=2.7.0和mpi4py=2.0.0对python版本有要求，因此采用**python3.6**
- h5py=2.7.0 -> python[version='>=2.7,<2.8.0a0|>=3.5,<3.6.0a0|>=3.6,<3.7.0a0']
- mpi4py=2.0.0的[wheel只提供到py35](https://pypi.org/project/mpi4py/2.0.0/#files)，但是py36也可以从源码编译（pip完成）
```shell
conda create -p /path/conda/envs/landscape -y python=3.6
conda activate /path/conda/envs/landscape
```
安装torch=1.10.2+cu111，原因见[1.10.X should be the last official PyTorch release to support Python 3.6](https://github.com/pytorch/pytorch/issues/66462)

```shell
pip install torch==1.10.2+cu111 torchvision==0.11.3+cu111 torchaudio==0.10.2 -f https://download.pytorch.org/whl/cu111/torch_stable.html
```
