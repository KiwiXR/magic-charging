# 在conda环境中安装指定版本的cuda runtime和nvcc

- [前置知识](#前置知识)
- [实际操作](#实际操作)
  - [安装启用 cuda 的 pytorch](#安装启用-cuda-的-pytorch)
  - [在 conda 环境中安装特定版本的 nvcc 用于编译（必须满足cuda runtime 和 cuda driver 兼容性中的要求）](#在-conda-环境中安装特定版本的-nvcc-用于编译必须满足cuda-runtime-和-cuda-driver-兼容性中的要求)
  - [在系统中安装多个版本的 cuda(nvcc) 并通过软链接或环境变量切换](#在系统中安装多个版本的-cudanvcc-并通过软链接或环境变量切换)
- [补充知识](#补充知识)
  - [cuda 生态介绍](#cuda-生态介绍)
  - [cuda runtime 和 cuda driver 兼容性](#cuda-runtime-和-cuda-driver-兼容性)
    - [小版本间的兼容：runtime \< driver 随便兼容，runtime \> driver 大版本内兼容](#小版本间的兼容runtime--driver-随便兼容runtime--driver-大版本内兼容)
    - [大版本间的前向兼容需要打 compat 补丁（图片背景是透明的）](#大版本间的前向兼容需要打-compat-补丁图片背景是透明的)

## 前置知识

1. `nvidia-smi`中显示的CUDA Version，其全称为CUDA Driver Version，即当前驱动的版本号，也即当前GPU驱动所支持的最高cuda版本（例外是下面的[小版本兼容](#小版本间的兼容runtime--driver-随便兼容runtime--driver-大版本内兼容)）
2. `nvidia-smi`是driver API
3. `nvcc -V`中显示的是CUDA Runtime Version，即使用当前nvcc编译器，所得到的应用程序需要基于的runtime版本号
4. `torch.version.cuda`显示的是用于运行该torch时所需要的CUDA Runtime Version（即编译时所用nvcc版本）

## 实际操作

### 安装启用 cuda 的 pytorch

在安装torch时，实际上已经随附cuda runtime了，以torch v1.11.0 + cuda 11.3为例，用conda和pip安装的[官方推荐命令](https://pytorch.org/get-started/previous-versions/#v1110)分别是：

``` bash
conda install pytorch==1.11.0 torchvision==0.12.0 torchaudio==0.11.0 cudatoolkit=11.3 -c pytorch
```

以及

``` bash
pip install torch==1.11.0+cu113 torchvision==0.12.0+cu113 torchaudio==0.11.0 --extra-index-url https://download.pytorch.org/whl/cu113
```

这里面，conda额外安装的cudatoolkit就包含了cuda runtime，而pip安装的torch==1.11.0+cu113中也已经捆绑安装了cuda runtime，因此torch的运行时环境是完整的，不需要额外装cuda(nvcc)

### 在 conda 环境中安装特定版本的 nvcc 用于编译（必须满足[cuda runtime 和 cuda driver 兼容性](#cuda-runtime-和-cuda-driver-兼容性)中的要求）

常见需求如从源码编译安装 detectron2 等：`python -m pip install 'git+https://github.com/MaureenZOU/detectron2-xyz.git'`

由于上面已经安装好了 runtime ，所以只要安装对应的 nvcc 编译器，而它又属于 cudatoolkit-dev 包的一部分，因此可以直接通过 conda 安装（依旧以 cuda 11.3 为例）：

``` bash
conda install -c conda-forge cudatoolkit-dev=11.3 -y
```

但此时直接使用 `nvcc -V` 依旧使用的是系统的 nvcc ，因此需要通过环境变量来切换：

``` bash
export PATH=/path-to-conda-env/pkgs/cuda-toolkit/bin/:$PATH
export LD_LIBRARY_PATH=/path-to-conda-env/pkgs/cuda-toolkit/lib64:$LD_LIBRARY_PATH
export CUDA_HOME=/path-to-conda-env/pkgs/cuda-toolkit/
```

再次执行 `nvcc -V` ，发现已经切换到了环境中的 nvcc 版本

相比于在系统层面手动安装，该方式优点在于安装过程是全自动化的

**小trick：**

``` bash
export CUDA_HOME=$(dirname $(dirname $(which python)))/pkgs/cuda-toolkit
export PATH="$CUDA_HOME"/bin/:$PATH
export LD_LIBRARY_PATH="$CUDA_HOME"/lib64:$LD_LIBRARY_PATH
```

### 在系统中安装多个版本的 cuda(nvcc) 并通过软链接或环境变量切换

略，大体思路和上一个小标题一样，教程到处都是

## 补充知识

### cuda 生态介绍

一个较简单的科普：<https://chenglu.me/blogs/what-is-cuda>

一个很完整的科普，但不是完全正确（看看评论）：<https://zhuanlan.zhihu.com/p/91334380>

简单而言，cuda应用程序调用方式为 `application (-> cuda runtime API) -> cuda driver API -> cuda driver -> gpu hardware`，括号表示application可能直接调用driver API

![](https://pic3.zhimg.com/80/v2-a1f1d9f699697a8e05979abf749fbeae_720w.webp)

### cuda runtime 和 cuda driver 兼容性

关于driver兼容性的官方文档：<https://docs.nvidia.com/deploy/cuda-compatibility/>

#### 小版本间的兼容：runtime < driver 随便兼容，runtime > driver 大版本内兼容

> 2.3. Deployment Considerations for Minor Version Compatibility
>
> As described, applications that directly rely only on the CUDA runtime can be deployed in the following two scenarios:
>
>   1. **CUDA driver that’s installed on the system is newer than the runtime.**
>   2. **CUDA runtime is newer than the CUDA driver on the system but they are from the same major release of CUDA Toolkit.**
>
> In scenario 2, system admins should be aware of the aforementioned limitations and should be able to tell why an application may be failing if they run into any issues.
>
> _Figure 3. NVRTC supports minor version compatibility from CUDA 11.3 onwards_
> ![NVRTC supports minor version compatibility from CUDA 11.3 onwards](https://docs.nvidia.com/deploy/cuda-compatibility/graphics/NVRTC-compatibility11.3.png)

#### 大版本间的前向兼容需要打 compat 补丁（图片背景是透明的）

> 3.1. Forward Compatibility Support Across Major Toolkit Versions
>
> Increasingly, data centers and enterprises may not want to update the NVIDIA GPU Driver across major release versions due to the rigorous testing and validation that happens before any system level driver installations are done.
>
> To support such scenarios, CUDA introduced a Forward Compatibility Upgrade path in CUDA 10.0.
>
> _Figure 4. Forward Compatibility Upgrade Path_
>
> ![Forward Compatibility Upgrade Path](https://docs.nvidia.com/deploy/cuda-compatibility/graphics/forward-compatibility-upgrade-path.png)
>
> **Forward Compatibility is applicable only for systems with NVIDIA Data Center GPUs or select NGC Server Ready SKUs of RTX cards.** It’s mainly intended to support applications built on newer CUDA Toolkits to run on systems installed with an older NVIDIA Linux GPU driver from different major release families. This new forward-compatible upgrade path requires the use of a special package called “CUDA compat package”.
