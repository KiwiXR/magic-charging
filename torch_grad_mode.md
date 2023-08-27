# torch中grad mode, no-grad mode，inference mode和eval()的区别

- grad mode是默认的mode，梯度正常传播
  - 只有在这一模式中， `requires_grad` 才会生效
  - ``` python
    model.requires_grad_(False)
    ```
    ``` python
    param.requires_grad = False
    ```
- no-grad mode中所有梯度将会关闭（包括输入和参数），从而不能够回传，但是其输出可以继续进入计算图
  - 在进入这一context时， `requires_grad=False` ；在退出这一context时， `requires_grad=True`
  - ``` python
    with torch.no_grad():
        pass
    ```
    ``` python
    @torch.no_grad()
    def foo():
        pass
    ```
- inference mode是no-grad mode的一种极端情况，不仅不会记录梯度信息，其中创建tensor时会进一步丢失梯度记录，从而无法再进入计算图
  - 在data processing和model evaluation时可以大大加速而没有副作用
  - ``` python
    with torch.inference_mode():
        pass
    ```
    ``` python
    @torch.inference_mode()
    def foo():
        pass
    ```
- evaluation mode（ `torch.eval()` ）其实和以上没有任何关系，其作用是调用 `nn.Module.eval()` ，每个模块有着不同的响应
  - 特别地，可以用来关闭 `torch.nn.Dropout` 和冻结 `torch.nn.BatchNorm2d` 中的统计量，这在validation/testing阶段会比较有用
  - ``` python
    model.eval()
    ```

注：为了冻结模型，一种可行的方案是
```python
model.requires_grad_(False)
model.eval()
y = model(x)
```
这样，梯度只会沿着输入`x`传播（即反向传播到前面是可能的），可以在冻结模型时节省显存
