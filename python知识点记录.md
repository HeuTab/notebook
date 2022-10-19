# tqdm

```python
# 向tqdm中传入迭代类型即可
from tqdm import tqdm
import time

text = ""
for char in tqdm(["a", "b", "c", "d"]):
    time.sleep(0.25)
    text = text + char

```



```python
# 使用with语句来控制tqdm的更新
with tqdm(total=100) as pbar:
    for i in range(10):
        time.sleep(0.01)
        pbar.update(1)  # 每次更新的多少
       
# 当然也可以不用with，将tqdm赋值给一个变量
pbar = tqdm(total=100)
for i in range(100):
    time.sleep(0.1)
    pbar.update(10)
pbar.close()  # ！！ 注意这样使用之后必须调用del 或者close方法删除该变量

```

```
with tqdm(total=100) as pbar
可以理解为：
pbar = tqdm(total=100)
```

