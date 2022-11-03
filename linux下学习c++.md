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
编译每一个文件夹下面的文件都需要有一个CMakeLists.txt文件
```

```
删除CMakeLists.txt文件里面的所有内容
echo "" > CMakeList.txt
```

```
如果想重新生成main，输入make clean就可以删除main这个elf文件。
```

```
project (hello)

set(SRC_LIST main.cpp ./testFun.cpp)

```

## cmake中的关键字

### 1. 同一目录下的多个源文件

#### aux_source_directory

```
使用aux_source_directory把当前目录下的源文件列表存放到SRC_LIST里，然后在add_executable里调用SRC_LIST

cmake_minimum_required (VERSION 2.8)
project (demo)
aux_source_directory(. SRC_LIST)
add_executable(main ${SRC_LIST})

```

#### set

```
但是aux_source_directory也存在弊端，他会把指定目录下的所有源文件都加进来，可能会加入一些我们不需要的文件，此时我们可以使用set命令去新建变量来存放需要的源文件

cmake_minimum_required (VERSION 2.8)
project (demo)
set( SRC_LIST
	 ./main.c
	 ./testFunc1.c
	 ./testFunc.c)
add_executable(main ${SRC_LIST})

```

### 2. 不同目录下的多个源文件

#### include_directories

```
文件结构如下：
CMakeList.txt
main.c
test_func
	testFunc.c
	testFunc.h
test_func1
	testFunc1.c
	testFunc2.h
```

```
cmake_minimum_required (VERSION 2.8)
project (demo)

include_directories (test_func test_func1)

aux_source_directory (test_func SRC_LIST)
aux_source_directory (test_func1 SRC_LIST1)

add_executable (main main.c ${SRC_LIST} ${SRC_LIST1})

```

这里出现了一个新的命令：**include_directories**。该命令是用来向工程添加多个指定头文件的搜索路径，路径之间用空格分隔。
因为main.c里include了testFunc.h和testFunc1.h，如果没有这个命令来指定头文件所在位置，就会无法编译。当然，也可以在main.c里使用include来指定路径，如下

## cmake中的正规组织结构

```
bin：存放最终输出的elf文件
build：存放生成的对象文件
include：存放头文件(.h)
src：存放源文件(.c)
```

在最外层目录下新建一个CMakeLists.txt，内容如下：

```
cmake_minimum_required (VERSION 2.8)

project (demo)

add_subdirectory (src)

```

这里出现一个新的命令add_subdirectory()，这个命令可以向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制的存放位置，具体用法可以百度。

这里制定了src目录下存放了源文件，当执行cmake时，就会进入src目录下去找src目录下的CMakeLists.txt，所以在src目录下也建立了一个CMakeLists.txt，内容如下：

```
aux_source_directory (. SRC_LIST)

include_directories (../include)

add_executable (main ${SRC_LIST})

set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

```

这里又出现了一个新的命令set，是用于定义变量的，EXECUTABLE_OUT_PATH和PROJECT_SOURCE_DIR是CMake自带的预定义变量，其意义如下：

- EXECUTABLE_OUT_PATH：目标二进制可执行文件的存放位置
- PROJECT_SOURCE_DIR：工程的根目录

所以，这里set的意思是把存放elf文件的位置设置为工程根目录下的bin目录。

下面来运行cmake，不过得在build目录下，然后输入以下命令：

```sh
cmake ..
```

Makefile会在build目录下生成，然后在build目录下运行make

*解释一下为什么在build目录下运行cmake。*

<u>如果不这样做，cmake运行时生成的附带文件就会跟源码文件混在一起，这样会对程序的目录结构造成污染，而在build目录下运行cmake，生成的附带文件就只会待在build目录下，如果我们不想要这些文件了就可以直接清空build目录，非常方便。</u>

另外一种写法：

前面的工程使用了2个CMakeLists.txt文件，最外层的CMakeLists.txt用于掌控全局，使用add_subdirectory来控制其他目录下的CMakeLists.txt的运行。

也可以只用一个CMakeLists.txt问爱你，把最外层的CMakeLists.txt内容改写成下面的内容同时把src目录下面的CMakeLists.txt删除：

```
cmake_minimum_required (VERSION 2.8)

project (demo)

set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

aux_source_directory (src SRC_LIST)

include_directories (include)

add_executable (main ${SRC_LIST})

```







常见c++厂商

| 厂商 | C     | C++     | Fortran  |
| ---- | ----- | ------- | -------- |
| GNU  | gcc   | g++     | gfortran |
| LLVM | clang | clang++ | flang    |

```
g++ main.cpp -o a.out
g++ main.cpp --std=c++11 -o a.out
```

# cpp

```
函数的声明一般放到头文件中：*.h、*.hpp
函数的实现一般放到源文件中：*.c、*.cpp
```

## 1.1. 编译和链接

```c++
main.cpp

#include<iostream>
#include "mul.hpp"

using namespace std;

int main()
{
	int a,b;
	int result;
	
	cout << "Pick two integers:";
	cin >> a;
	cin >> b;
	
	result = mul(a,b);
	
	cout << "The result is " << result << endl;
	
	return 0;
}
```

```
mul.hpp

#pragma once（这是一个预处理指令）

int mul(int a,int b);
```

```
#include "mul.hpp"
int mul(int a,int b)
{
	return a * b;
}
```

![](./image/0001.jpg)

```
编译和链接过程如下：
main.cpp通过g++把它编译成main.o，.o文件为object的文件 是一个二进制文件
-c的意思是：只编译不链接
从object的文件生成可执行程序的步骤叫链接，实际上就是把两个二进制文件合并起来
```

常见三类错误：编译错误、链接错误、运行时错误

## 1.2. Preprocessor（预处理）

每个预处理指令占一行，而且只能占一行，如果预处理指令特别长，可以使用转义符转成一行

```
preprocessing instruction:
define,undef,include,if,ifdef,ifndef,else,elif,endif,line,error,pragma
```

![](./image/0002.jpg)

![](./image/0003.jpg)

## 1.3. argc和argv参数

```
#include<iostream>
using namespace std;
int main(int argc,char **argv)
{
	for(int i = 0 ;i<argc;i++){
		cout << i << ":" << argv[i] << endl;
	}
}
```

argc表示有几个参数，argv就是参数列表

## 2.1数值初始化方式

```
int num;
num = 10;

int num = 10;

int num (10);

int num {10};
```

unsigned int 和 int 的区别

```
signed int 最高位为符号位，0表示整数，1表示负数。取值范围为：[-2^31,2^31-1]
unsigned int 无符号位。取值范围为：[0,2^32-1]
```

查看在C/C++中每种数据类型占多少位：https://en.cppreference.com/w/cpp/language/types

```
查看数据类型占多少字节
sizeof(int)
```



```
since C++11 定义数据的方式
#include<cstdint>
int8_t
int16_t
int32_t
int64_t
uint8_t
uint16_t
uint32_t
uint64_t
```

```
some useful macros 查看最大数和最小数
#include<cstdint>
INT8_MIN
INT16_MIN
INT32_MIN
INT64_MIN
INT8_MAX
INT16_MAX
INT32_MAX
INT64_MAX
```



![](./image/0004.jpg)

```
float f1 = 2.34E+10f;
float f2 = f1 + 10;
此时f2==f1，因为浮点数是间隔采样的，不能连续的表示一个数值，所以需要达到下一个采样点的界限才能真正的增长

所以我们如果我们要比较两个浮点数的大小的话，最好用
if(f1 == f2)//bad
if(fabs(f1-f2) < FLT_EPSILON)//good
```

## 2.2 算数运算

### 常数数值定义

```
95		//decimal
0137	//octal
0x5F	//hexadecimal

95		//int
95u		//unsigned int
95l		//long
95ul	//unsigned long
95lu	//unsigned long

3.14159	//3.14159
6.02e23	//6.02*10^23
1.6e-19	//1.6*10^-19
3.0		//3.0

6.02e23L	//long double
6.02e23f	//float
6.02e23		//double
```

### const type qualifier

```
const float PI = 3.1415923f;
必须在定义的时候进行初始化；不能修改
```

### arithmetic operators

| Operator name       | Syntax |
| ------------------- | ------ |
| bitwise NOT         | ~a     |
| bitwise AND         | a & b  |
| bitwise OR          | a \| b |
| bitwise XOR         | a ^ b  |
| bitwise left shift  | a << b |
| bitwise right shift | a >> b |

3.1
