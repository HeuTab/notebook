# dgl文档阅读

```python
import dgl
import torch as th

u, v = th.tensor([0, 0, 0, 1]), th.tensor([1, 2, 3, 4])

g = dgl.graph((u, v))
print(g)
print(g.nodes())
print(g.edges())
print(g.edges(form='all'))

g = dgl.graph((u, v), num_nodes=8)
print(g)
print(g.nodes())
print(g.edges())

```

