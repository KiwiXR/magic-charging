# 迁移conda环境

- [通用（亦适用于不同平台和操作系统）【使用conda-env】](#通用亦适用于不同平台和操作系统使用conda-env)
- [相同操作系统【使用conda core】](#相同操作系统使用conda-core)
- [相同操作系统（离线）【使用conda-pack】](#相同操作系统离线使用conda-pack)

## 通用（亦适用于不同平台和操作系统）【使用conda-env】

- src（**首选**，能解决跨OS的build兼容性问题 [[ref]](https://github.com/conda/conda/issues/9399)）
  - 当前环境

    ```bash
    conda env export --no-builds > env.yml
    ```

  - 指定环境

    ```bash
    conda env export -n srcname --no-builds > env.yml
    ```

- src（不跨OS）
  - 当前环境

    ```bash
    conda env export > env.yml
    ```

  - 指定环境

    ```bash
    conda env export -n srcname > env.yml
    ```

- tgt （新环境，指定环境名）

  ```bash
  conda env create -n newname -f env.yml
  ```

- tgt （新环境，沿用环境名，环境名由 `env.yml` 第一行 `name: envname`给定）

  ```bash
  conda env create -f env.yml
  ```

  如果不在默认位置部署，**一定**要加 `-p` 参数！

- tgt （旧环境）

  ```bash
  conda env update -f env.yml
  ```

  如果不在默认位置部署，**一定**要加 `-p` 参数！

## 相同操作系统【使用conda core】

- src（`-e` 指代 `--export` 而非 `--explicit`）
  - 当前环境

    ```bash
    conda list -e > env-list.txt
    ```

  - 指定环境

    ```bash
    conda list -e -n srcname > env-list.txt
    ```

- tgt （新环境，**必须**指定环境名，`--file` **不能**缩写）

  ```bash
  conda create -n newname --file env-list.txt
  ```

- tgt （旧环境，`--file` **不能**缩写）

  ```bash
  conda install --file env-list.txt -y
  ```

## 相同操作系统（离线）【使用conda-pack】

- src

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

- tgt

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
[[conda-env v.s. conda]](https://github.com/conda/conda/issues/5253)

```bash
$ conda -V
conda 22.9.0
```
