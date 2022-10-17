# node2vec

## skip-gram模型

```
推荐视频：
https://www.bilibili.com/video/BV1HY41187Jj/?spm_id_from=333.337.search-card.all.click&vd_source=52a7b0d5b85b1f6082b64a60d2bc5dc0
```

## 练习代码

```
import torch
from torch_geometric.data import Data

import collections

text = ['2', 'x', '3', 'r', '2']
decoder = [word for word in text]
print(decoder)

encoder = {word: i for i, word in enumerate(decoder)}
print(encoder)

data = [encoder[word] for word in text]
print(data)

```

