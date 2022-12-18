# tensorboard

```
from torch.utils.tensorboard import SummaryWriter
或者
from tensorboardX import SummaryWriter

tb_writer = SummaryWriter(log_dir="./log") # 实例化tensorboard对象,log_dir是将tensorboad对象保存在哪里

tb_writer.add_graph(model, init_data) # 这行代码的作用是创建模型的结构图，model参数为模型的名称，init_data为输入到模型的数据，因为会根据数据的正向传播的流程来创建网络的结构图

tags = ["train_loss", "accuracy"]
tb_writer.add_scalar(tags[0], loss, epoch)
tb_writer.add_scalar(tags[1], acc, epoch) # 三个参数分别为tag标签，第二个参数为对应的值，第三个参数为训练到哪一步了，就是对应的epoch


tb_writer.add_figure


tb_writer.add_histogram # 添加直方图，也可以查看对应的权重大小
tb_writer.add_histogram(tag="layer1/block0/conv1",
						values=model.layer1[0],conv.weight,
						global_step=epoch)
						
						
						
						
						
						
tensorboard --logdir=<directory_name>

需要注意的是，在windows路径下的路径为双\\
 tensorboard --logdir=G:\\github\\GammaGL\\zgy\\logs
```

