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

```shell
pip install mpi4py==2.0.0 numpy==1.15.1 h5py==2.7.0 matplotlib==2.0.2 scipy==0.19
```
