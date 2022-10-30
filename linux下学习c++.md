# cmake

## 在配置c++编译环境的时候需要安装gcc包，可能会出现安装不成功的情况

解决方案：

```
https://blog.csdn.net/beizhengren/article/details/77678603?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166704007516800182721209%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166704007516800182721209&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-77678603-null-null.142^v62^opensearch_v2,201^v3^control_2,213^v1^t3_control2&utm_term=%E6%97%A0%E6%B3%95%E5%AE%9A%E4%BD%8D%E8%BD%AF%E4%BB%B6%E5%8C%85&spm=1018.2226.3001.4187

操作source.list文件，在文件末尾添加
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse


然后执行
sudo apt-get update
```

## xshell连接不上linux可能的问题

```
可能是linux系统下没有安装ssh


#查看ssh服务安装情况
rpm -qa | grep ssh 
#未安装使用命令安装
sudo apt install ssh

即可解决
```

## 需要安装的包

```
gcc cmake g++
```

## 编译cmake

```
cmake .		进行编译，为了生成Makefile文件
make		最终使用Makefile文件进行编译

cmake .. 表示编译上一级目录
```

```
编译每一个文件夹下面的文件都需要有一个CMakeList.txt文件
```

```
删除CMakeList.txt文件里面的所有内容
echo "" > CMakeList.txt
```

```
如果想重新生成main，输入make clean就可以删除main这个elf文件。
```

```
project (hello)

set(SRC_LIST main.cpp ./testFun.cpp)
```

