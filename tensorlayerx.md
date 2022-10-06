# tensorlayerx学习随笔

## 0.一些单词含义

```
stddev:标准差
```



## 1.随机初始化一个矩阵

```python
init = tlx.nn.initializers.random_uniform(minval=1, maxval=1000)
data = init(shape=(1, 800), dtype=tlx.float32)
print(data)
```

