# torch snipplets

## torch中利用advanced indexing对某一维进行选择
> [torch follows numpy design](https://pytorch.org/docs/stable/tensor_view.html#:~:text=NOTE-,When,-accessing%20the%20contents)
> 
> [numpy advanced indexing](https://docs.scipy.org/doc/numpy-1.10.4/reference/arrays.indexing.html#advanced-indexing)
>
> [unsqueeze n times at dim d](https://github.com/pytorch/pytorch/issues/9410#issuecomment-758651563)

```python
def index_select_plus(input, index):
  assert input.dim() >= index.dim() and input.shape[:index.dim()-1] == index.shape[:index.dim()-1]
  index_list = []
  for i, sz in enumerate(index.shape[:-1]):
    ind_i = unsqueeze_n_times_at_dim(torch.arange(sz), n=index.dim()-i-1, dim=-1)
    index_list.append(ind_i)
  index_list.append(index)
  return input[index_list]


def unsqueeze_n_times_at_dim(input, n, dim):
  if dim < 0:
    # https://pytorch.org/docs/master/generated/torch.unsqueeze.html#torch.unsqueeze
    # Negative dim will correspond to unsqueeze() applied at dim = dim + input.dim() + 1.
    dim = dim + input.dim() + 1
  return input[(slice(None),) * dim + (None, ) * n]
```

```python
a = torch.randn(10, 11, 12)
b = torch.randint(0, 11, (10, 5))
print(index_select_plus(a, b).shape)
# torch.Size([10, 5, 12])

b = torch.randint(0, 12, (10, 11, 5))
print(index_select_plus(a, b).shape)
# torch.Size([10, 11, 5])
```
