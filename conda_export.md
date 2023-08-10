# 迁移conda环境

+ 通用（亦适用于不同平台和操作系统）【使用conda-env】
  + src（首选，能解决跨OS的build兼容性问题 [[ref]](https://github.com/conda/conda/issues/9399)）
    ```bash
    conda env export --no-builds > env.yml
    ``` 
  + src
    ```bash
    conda env export > env.yml
    ```
  + tgt (new env w/ name)
    ```bash
    conda env create -n newname -f env.yml
    ```
  + tgt (new env w/o name)
    ```bash
    conda env create -f env.yml
    ```
  + tgt (existing env)
    ```bash
    conda env update -f env.yml -y
    ```
+ 相同操作系统【使用conda core】
  + src
    ```bash
    conda list -e > env-list.txt # export
    ```
  + tgt (new env w/ name)
    ```bash
    conda create -n newname --file env-list.txt
    ```
  + tgt (existing env)
    ```bash
    conda install --file env-list.txt -y
    ```
+ 相同操作系统（离线）【使用conda-pack】
  + src
    ```bash
    conda activate base
    conda install -c conda-forge conda-pack
    #pip install conda-pack
    ```
    ```bash
    # Pack environment my_env into my_env.tar.gz
    $ conda pack -n my_env
    
    # Pack environment my_env into out_name.tar.gz
    $ conda pack -n my_env -o out_name.tar.gz
    
    # Pack environment located at an explicit path into my_env.tar.gz
    $ conda pack -p /explicit/path/to/my_env
    ```
  + tgt
    ```bash
    # Unpack environment into directory `my_env`
    $ mkdir -p my_env
    $ tar -xzf my_env.tar.gz -C my_env
    
    # Use Python without activating or fixing the prefixes. Most Python
    # libraries will work fine, but things that require prefix cleanups
    # will fail.
    $ ./my_env/bin/python
    
    # Activate the environment. This adds `my_env/bin` to your path
    $ source my_env/bin/activate
    
    # Run Python from in the environment
    (my_env) $ python
    
    # Cleanup prefixes from in the active environment.
    # Note that this command can also be run without activating the environment
    # as long as some version of Python is already installed on the machine.
    (my_env) $ conda-unpack
    ```

[[anaconda]](https://www.anaconda.com/blog/moving-conda-environments) 
[[zhihu]](https://zhuanlan.zhihu.com/p/87344422) 
[[csdn1]](https://blog.csdn.net/weixin_43913261/article/details/124687789) 
[[csdn2]](https://blog.csdn.net/IT_Novice_/article/details/125616956) 
