# Diffusers相关
## 如何离线加载模型
+ 使用 `HF_HUB_OFFLINE=1`
  + 方便，作为环境变量生效
+ 使用 `local_files_only=True`
  + 麻烦，需要在每个 `from_pretrained` 中都要加
