# dgl文档阅读

# 一、基本概念

## 1.1 1.2图的基本概念

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

转换为无向图

```python
src, dst = torch.tensor([0, 1, 2, 3]), torch.tensor([3, 4, 5, 6])
g = dgl.graph((src, dst))
print(g)
g = dgl.to_bidirected(g)
print(g)
```

相关接口方法

- dgl.graph()
- dgl.DGLGraph.nodes()
- dgl.DGLGraph.edges()
- dgl.to_bidirected()
- dgl.DGLGraph.int()
- dgl.DGLGraph.long()
- dgl.DGLGraph.idtype

## 1.3 节点与边的特征

关于ndata和edata的注意点:

- 特征值只能是数值类型, 但不局限于标量, 可以是向量与张量;
- 如果使用张量赋值, 则张量的第一维必须与节点或边的数量相同(即默认给每个节点或每条边都赋值, 而不能给图中部分节点或边赋值);
- 所有节点或所有边的同名特征必须具有同样的维度, 如不能部分节点的嵌入特征是256维, 其他的则是512维, 需要进行padding, 其实本质上就是要求张量赋值时必须是完整的多面体, 不能边边角角缺了一些;

- 特征值只能是数值类型，但不局限于标量，可以是向量与张量
- 如果使用张量赋值，则张量的第一维必须与节点或边的数量相同（即默认给每个节点或每条边都赋值，而不能给图中部分节点或边赋值）
- 所有节点或所有边的同名特征必须具有同样的维度，如不能部分节点的嵌入特征是256维，其他的则是512维，需要进行padding，其本质上就是要求张量赋值时必须是完整的多面体，不能边边角角缺了一些



相关接口方法：

- dgl.DGLGraph.ndata
- dgl.DGLGraph.edata



## 1.4 从外部源创建图

### 1.从scipy稀疏矩阵创建图

```python
import dgl
import torch
import scipy.sparse as sp

spmat = sp.rand(100, 100, density=0.05)  # 表示有100个节点，邻接矩阵维100*100，然后邻接矩阵有边的概率维0.05，所以共100个点、500条边
print(spmat)
print(dgl.from_scipy(spmat))

```

### 2.从networkx图创建图

```python
import networkx as nx
import dgl

nx_g = nx.path_graph(5)  # 0-1-2-3-4
print(dgl.from_networkx(nx_g))
nxg = nx.DiGraph([(2, 1), (1, 2), (2, 3), (0, 0)])
print(dgl.from_networkx(nxg))

```

相关接口方法：

- dgl.from_scipy()
- dgl.from_networkx()



## 1.5 异构图

### 1. 创建异构图

```python
import dgl
import torch as th

graph_data = {
    ('drug', 'interacts', 'drug'): (th.tensor([0, 1]), th.tensor([1, 2])),
    ('drug', 'interacts', 'gene'): (th.tensor([0, 1]), th.tensor([2, 3])),
    ('drug', 'treats', 'disease'): (th.tensor([1]), th.tensor([2]))
}
g = dgl.heterograph(graph_data)
print(g.ntypes)
print(g.etypes)
print(g.canonical_etypes)
print(g)
print(g.metagraph().edges())
print(g.num_nodes())
print(g.num_nodes('drug'))
print(g.nodes('drug'))

```

```
['disease', 'drug', 'gene']
['interacts', 'interacts', 'treats']
[('drug', 'interacts', 'drug'), ('drug', 'interacts', 'gene'), ('drug', 'treats', 'disease')]
Graph(num_nodes={'disease': 3, 'drug': 3, 'gene': 4},
      num_edges={('drug', 'interacts', 'drug'): 2, ('drug', 'interacts', 'gene'): 2, ('drug', 'treats', 'disease'): 1},
      metagraph=[('drug', 'drug', 'interacts'), ('drug', 'gene', 'interacts'), ('drug', 'disease', 'treats')])
[('drug', 'drug'), ('drug', 'gene'), ('drug', 'disease')]
10
3
tensor([0, 1, 2])

```

异构图中多出了**metagraph**,本质是记录该异构图中所有不重复的三元组

相关接口：

- dgl.heterograph()
- ntypes
- etypes
- canonical_etypes
- metagraph
- num_nodes()：不加参数就是所有节点数，可以添加参数找出特定名称的节点总数；
- nodes()：必须加参数（节点名称），返回所有该节点的编号张量

### 2. 异构图的特征赋值

```python
g.nodes['drug'].data['hv'] = th.ones(3, 1)
g.edges['treats'].data['he'] = th.zeros(1, 1)

```

### 3. 异构图的子图：从边来去子图

```python
import dgl
import torch as th

graph_data = {
    ('drug', 'interacts', 'drug'): (th.tensor([0, 1]), th.tensor([1, 2])),
    ('drug', 'interacts', 'gene'): (th.tensor([0, 1]), th.tensor([2, 3])),
    ('drug', 'treats', 'disease'): (th.tensor([1]), th.tensor([2]))
}
g = dgl.heterograph(graph_data)
print(g)
subg = dgl.edge_type_subgraph(g, [('drug', 'interacts', 'drug'),
                                  ('drug', 'treats', 'disease')])
print(subg)
subg2 = dgl.node_type_subgraph(g, ['drug', 'gene'])
print(subg2)

```

### 4. 异构图转为同构图

```python
import dgl
import torch as th

g = dgl.heterograph({
   ('drug', 'interacts', 'drug'): (th.tensor([0, 1]), th.tensor([1, 2])),
   ('drug', 'treats', 'disease'): (th.tensor([1]), th.tensor([2]))})
g.nodes['drug'].data['hv'] = th.zeros(3, 1)
g.nodes['disease'].data['hv'] = th.ones(3, 1)
g.edges['interacts'].data['he'] = th.zeros(2, 1)
g.edges['treats'].data['he'] = th.zeros(1, 2)
# By default, it does not merge any features
hg = dgl.to_homogeneous(g)
print('hv' in hg.ndata)

# Copy node features
hg = dgl.to_homogeneous(g, ndata=['hv'])
print(hg.ndata['hv'])

# Copy edge features
# For feature copy, it expects features to have
# the same size and dtype across node/edge types
# hg = dgl.to_homogeneous(g, edata=['he'])

# Order of node types in the heterograph
print(g.ntypes)
# Original node types
print(hg.ndata[dgl.NTYPE])
# Original type-specific node IDs
print(hg.ndata[dgl.NID])
# Order of edge types in the heterograph
print(g.etypes)
# Original edge types
print(hg.edata[dgl.ETYPE])
# Original type-specific edge IDs
print(hg.edata[dgl.EID])

```

原始节点或边的类型和对应的ID被存储在**ndata**和**edata**中

### 5. 模型的保存与加载

```
dgl.save_graphs(filename,g_list,labels=None)
g_list是一个list，可以存放多个图，如[g1,g2]
labels是一个string2tensor的字典
```

```python
import dgl
import torch as th

g1 = dgl.graph(([0, 1, 2], [1, 2, 3]))
g2 = dgl.graph(([0, 2], [2, 3]))
g2.edata["e"] = th.ones(2, 4)

from dgl.data.utils import save_graphs

graph_labels = {"glabel": th.tensor([0, 1])}
save_graphs("./data.bin", [g1, g2], graph_labels)

```

```
dgl.load_graphs(filename,idx_list=None)
当保存时是保存了多个图的话，就需要定义idx_list以便于区分不同的图了，这是一个整数列表
返回值为graph_list和labels，后者即保存时定义的labels
```

```python
from dgl.data.utils import load_graphs
glist, label_dict = load_graphs("./data.bin") # glist will be [g1, g2]
glist, label_dict = load_graphs("./data.bin", [0]) # glist will be [g1]

```

## 1.6 在GPU上使用graph

有两种方法在gpu上创建graph

- 用两个已经存储在gpu上的tensor来创建DGLGraph
- 使用dgl.DGLGraph.to(device)方法将DGLGraph移动到指定device的cuda上

```python
import dgl
import torch as th

u, v = th.tensor([0, 1, 2]), th.tensor([2, 3, 4])
g = dgl.graph((u, v))
g.ndata['x'] = th.randn(5, 3)
print(g.device)

cuda_g = g.to('cuda:0')
print(cuda_g.device)
print(cuda_g.ndata['x'].device)

u, v = u.to('cuda:0'), v.to('cuda:0')
g = dgl.graph((u, v))
print(g.device)

```

```
cpu
cuda:0
cuda:0
cuda:0

```

任何设计GPU图的操作都是在GPU上运行的；因此，这要求所有张量参数都已经放在GPU上，其结果（图或张量）也将在GPU上；此外，GPU图只接受GPU上的特征数据；

```
print(cuda_g.in_degrees())
print(cuda_g.in_edges([2, 3, 4]))   # ok for non-tensor type arguments
print(cuda_g.in_edges(th.tensor([2, 3, 4]).to('cuda:0')))  # tensor type must be on GPU
cuda_g.ndata['h'] = th.randn(5, 4)  # ERROR! feature must be on GPU too!

```

```
tensor([0, 0, 1, 1, 1], device='cuda:0')
(tensor([0, 1, 2], device='cuda:0'), tensor([2, 3, 4], device='cuda:0'))
(tensor([0, 1, 2], device='cuda:0'), tensor([2, 3, 4], device='cuda:0'))
DGLError: Cannot assign node feature "h" on device cpu to a graph on device cuda:0. Call DGLGraph.to() to copy the graph to the same device.

```

# 二、消息传递

