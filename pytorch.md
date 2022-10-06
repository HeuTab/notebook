# 1.dataset

```python
import os

dir_path = "文件路径"
data_path_list = os.listdir(dir_path)
data_path_list[0] #查看第一个文件名

# 第二种写法
root_dir = "大文件夹的路径"
label_dir = "大文件夹下面的一个小文件夹的路径"
path = os.path.join(root_dir,label_dir)
```

# 2.tensorboard

```python
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter("logs")

for i in range(100):
    writer.add_scalar("y = x", i, i)

writer.close()
```

这样就会在文件夹下面创建一个叫logs的文件夹，里面存放tensorboar生成的文件

```
tensorboard --logdir=logs --port=6007
#port默认是6006，logs和上面的SummaryWriter里的内容对应
```

# 3.optim

```python
import torch
from torch.nn import L1Loss
import torch.optim
from torch import nn

loss = nn.CrossEntropyLoss()
model = Model()
optim = torch.optim.SGD(model.parameters(), lr=0.01)
for epoch in range(10):
    running_loss = 0.0
    for data in dataloader:
        imgs, targets = data
        outputs = model(imgs)
        result_loss = loss(outputs, targets)
        
        optim.zero_grad()
        result_loss.backward()
        optim.step()
        
        running_loss += result_loss
    print(running_loss)

```

# 4.修改现有模型结构

```python
import torchvision

from torch import nn

vgg16_false = torchvision.models.vgg16(pretrained=False)
vgg16_true = torchvision.models.vgg16(pretrained=True)

print(vgg16_true)

train_data = torchvision.datasets.CIFAR10('../data', train=True, transform=torchvision.transforms.ToTensor(),
                                          download=True)

vgg16_true.classifier.add_module('add_linear', nn.Linear(1000, 10))
print(vgg16_true)

print(vgg16_false)
vgg16_false.classifier[6] = nn.Linear(4096, 10)
print(vgg16_false)
```

# 5.模型的保存与读取

```python
import torch
import torchvision

vgg16 = torchvision.models.vgg16(pretrained=False)

# 保存方式1：模型结构+模型参数
torch.save(vgg16, "vgg16_method1.pth")

# 保存方式2：模型参数（官方推荐）
torch.save(vgg16.state_dict(), "vgg16_method2.pth")

```

```python
import torchvision
import torch

# 方式1加载模型
model = torch.load("vgg16_method1.pth")
print(model)

# 方式2加载模型
vgg16 = torchvision.models.vgg16(pretrained=False)
vgg16.load_state_dict(torch.load("vgg16_method2.pth"))
model = torch.load("vgg16_method2.pth")
print(vgg16)

```

```
陷阱：当我们加载自己设计的模型的时候，需要在加载模型的地方引入我们的模型结构
from model_save import *
或
class model(nn.Module):
	...
```

# 6.正确率的计算

```
accuracy = (outputs.argmax(1) == target).sum()
outputs是模型的输出结果
target是标签
```

# 7.利用gpu进行训练

```
将
	1.网络模型
	2.数据（输入，标注）
	3.损失函数
加上.cuda()

例如：
model = Model()
if torch.cuda.is_available():
	model = model.cuda()
	
if torch.cuda.is_available():
	imgs = imgs.cuda()
	targets = targets.cuda()
	
if torch.cuda.is_available():
	loss_fn = loss_fn.cuda()
```

```
定义训练的设备
device = torch.device("cpu")
device = torch.device("cuda:0")
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

model = model.to(device)
loss_fn = loss_fn.to(device)
imgs = imgs.to(device)
targets = targets.to(device)
```



# 8.google colab

```
每周好像是有30小时是免费的
如果要是想运行terminal语句的话就在前面加一个感叹号，比如!nvidia-smi
```

# 9.训练技巧

```
model.train()	#切换至训练模式
train...
model.eval()	#切换至测试模式
with torch.no_grad():	#可以增加测试性能
	output = model(input)
```

# 10.GPU与显卡的联系

```
显卡->驱动->cuda->pytorch->计算机

有了驱动计算机才能识别显卡
```

# 11.知识点

```
如果是叶子节点并且required_grad=True的话，需要进行求导
```

