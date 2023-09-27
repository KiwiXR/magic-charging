# 在Conda环境中安装指定版本的CUDA Runtime和NVCC

## 前置知识

一个有些过时的科普：https://chenglu.me/blogs/what-is-cuda

关于driver兼容性的官方文档：https://docs.nvidia.com/deploy/cuda-compatibility/

### 小版本间的兼容：runtime < driver随便兼容，runtime > driver大版本内兼容
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

### 大版本间的前向兼容需要打compat补丁（图片背景是透明的）
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

在安装cuda时

``` bash
export PATH=/data2/lmj/conda/envs/internimage/pkgs/cuda-toolkit/bin/:$PATH
export LD_LIBRARY_PATH=/data2/lmj/conda/envs/internimage/pkgs/cuda-toolkit/lib64:$LD_LIBRARY_PATH
export CUDA_HOME=/data2/lmj/conda/envs/internimage/pkgs/cuda-toolkit/
```