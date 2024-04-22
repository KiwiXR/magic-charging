# Diffusers相关

# 下载模型

> https://padeoe.com/huggingface-large-models-downloader/#more-813
>
> https://huggingface.co/docs/huggingface_hub/guides/download

```python
from huggingface_hub import snapshot_download

repo_id = "runwayml/stable-diffusion-v1-5"

snapshot_download(repo_id, ignore_patterns=["*.msgpack", "*.h5", "*onnx*"], resume_download=True)
```

## 如何离线加载模型
+ 使用 `HF_HUB_OFFLINE=1`
  + 方便，作为环境变量生效
+ 使用 `local_files_only=True`
  + 麻烦，需要在每个 `from_pretrained` 中都要加

## 将下载好的模型转换为本地模型

```shell
HF_HUB_OFFLINE=1 python
```

safetensors非常快
> https://huggingface.co/docs/safetensors/speed

```python
from diffusers import DiffusionPipeline

repo_id = "runwayml/stable-diffusion-v1-5"

stable_diffusion = DiffusionPipeline.from_pretrained(repo_id, safety_checker=None, use_safetensors=True)

stable_diffusion.save_pretrained("./stable-diffusion-v1-5/", safe_serialization=True)  # safetensors, recommended
# stable_diffusion.save_pretrained("./stable-diffusion-v1-5/")  # bin

sd = DiffusionPipeline.from_pretrained('./stable-diffusion-v1-5/', safety_checker=None)
# sd = DiffusionPipeline.from_pretrained('./stable-diffusion-v1-5/', torch_dtype=torch.float16, safety_checker=None)  # fp16
```
