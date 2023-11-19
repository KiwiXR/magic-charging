# wandb 命令行用法

以执行 `train.py` 为例，一种在网络状况不稳定情况下的稳健方案为：
+ 首先考虑上传到wandb cloud还是wandb local server，两者都需要指定 `WANDB_API_KEY=`，wandb local server需要额外指定 `WANDB_BASE_URL=` （默认值为 `WANDB_BASE_URL="https://api.wandb.ai"` 即wandb cloud）
  + 以上需要在下方每一条指令前加上，或者直接采用 `export WANDB_API_KEY=` 的方式加入环境变量
    + ```shell
      export WANDB_BASE_URL=xxx
      export WANDB_API_KEY=xxx
      ```
+ 在运行目录下执行 `wandb init`，可以提前指定 `project`、`team` 等（local server需氪金才有team），这一配置在 `wandb/settings` 中体现
  + ```shell
    WANDB_BASE_URL=xxx WANDB_API_KEY=xxx wandb init
    ```
    或者，在采用 `export` 方式时，可以直接执行
    ```shell
    wandb init
    ```
+ 运行代码时，指定环境变量 `WANDB_MODE=offline`（这一环境变量默认为 `WANDB_MODE=online`）
  + ```shell
    WANDB_MODE=offline python train.py
    ```
+ 运行完毕后，结果目录会命名为 `wandb/offline-xxx`，此时可以用 `wandb sync` 查看同步情况
  + ```shell
    WANDB_BASE_URL=xxx WANDB_API_KEY=xxx wandb sync
    ```
+ 使用 `wandb sync wandb/offline-xxx` 可以同步单次运行结果，**注意环境变量**
+ 使用 `wandb sync --sync-all` 可以直接同步当前运行目录下所有结果，**注意环境变量**
