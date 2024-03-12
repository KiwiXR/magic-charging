# tensor.to()丢失tensor信息，得到全0的Tensor

现象：将tensor从一张卡移动到另一张卡时，tensor元素全变为0

```python
import torch
a = torch.ones(1).cuda(0)
print(a)
# tensor([1.], device='cuda:0')
b = a.to(1)
print(b)
# tensor([0.], device='cuda:1')
```

这可能是由于显卡间的PCIe线开启了ACS（或IOMMU，未知两者关系）

> https://github.com/pytorch/pytorch/issues/84803
> https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/troubleshooting.html#pci-access-control-services-acs

一次性关闭ACS的方法为（请确保知道自己在做什么，不知道也至少要看懂nvidia的文档内容）：

```bash
for BDF in `lspci -d "*:*:*" | awk '{print $1}'`; do
  # skip if it doesn't support ACS
  sudo setpci -v -s ${BDF} ECAP_ACS+0x6.w > /dev/null 2>&1
  if [ $? -ne 0 ]; then
    continue
  fi
  sudo setpci -v -s ${BDF} ECAP_ACS+0x6.w=0000
done
```
