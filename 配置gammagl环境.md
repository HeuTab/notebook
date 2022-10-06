# GammaGL环境配置

```
！！！重点：python3.7.5，cuda11.1
安装前先看paddle和torch和tensorflow支持哪些cuda版本
```

```
paddlepaddle支持的cuda版本：10.1、10.2、11.1、11.2、11.6
torch只支持1.10系列的
```



```bash
1.使用conda创建指定python版本环境
conda create -n gammagl python=3.7.5
2.更新bashrc中的环境变量
conda init bash && source /root/.bashrc
3.切换到创建的虚拟环境：gammagl
source activate gammagl
```



## 查看cuda信息

```
nvidia-smi
nvcc -V

然后去pytorch官网安装与环境相匹配的torch版本
```



## 查看torch信息

```python
import torch
torch.__version__
torch.version.cuda
```

