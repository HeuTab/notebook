# 🔴dgl&pyg&gammagl学习笔记

```python
import torch
from torch_geometric.data import Data

train_mask = torch.tensor([True, True, False, False])
y = torch.tensor([1, 2, 3, 4])
y_train = y[train_mask]
print(y_train)
```

# Planetoid

```
planetoid就是torch_geometric.datasets中的一个类。利用planetoid类可以处理三个数据集：cora、citeseer、pubmed。
dataset = Planetoid(root='./data',name='Cora')
```

