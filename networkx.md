# networkx

## 展示图的方法

```
import networkx as nx
import matplotlib.pyplot as plt

edges = [(1, 2), (1, 5), (2, 4), (4, 3), (3, 1)]
G = nx.DiGraph()  # 初始化有向图
G.add_edges_from(edges)  # 通过边集加载数据

print(G.nodes)  # 打印所有节点
print(G.edges)  # 打印所有边

nx.draw(G)  # 画图
plt.show()  # 显示

```

```python
import dgl
import torch
import networkx as nx
import matplotlib.pyplot as plt

src, dst = torch.tensor([0, 1, 2, 3]), torch.tensor([3, 4, 5, 6])
g = dgl.graph((src, dst))
g = g.to_networkx().to_undirected()
nx.draw(g)
plt.show()
```

```python
import dgl
import torch
import networkx as nx
import matplotlib.pyplot as plt

src, dst = torch.tensor([0, 1, 2, 3]), torch.tensor([3, 4, 5, 6])
g = dgl.graph((src, dst))
g = g.to_networkx().to_undirected()
pos = nx.kamada_kawai_layout(g)
nx.draw(g, pos, with_labels=True, node_color=[[.7, .7, .7]])
plt.show()

```

