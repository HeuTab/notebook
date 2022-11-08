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

## 3.1 分支语句

```
factor = isPositive ? 1 : -1;
factor = (isPOsitive) * 2 - 1;
建议使用下面的用法，因为下面没有使用跳转语句，效率更加高效
```

## 4.1 string

```
string定义方式：
char name1[] = "ABCD";
const wchar_t[] s = L"ABCD";
const char16_t[] s = u"ABCD";	//since c++11
const char32_t[] s = U"ABCD";	//since c++11
```

```
Copy:
	char* strcpy(char* dest, const char* src);//存在长度溢出的情况
	char* strncpy(char *dest, const char *src, size_t count);//count表示最多复制多少个字符，可以设置为min(dest,src)

Concatenate:appends a copy of src to dest
	char *strcat(char *dest, const char *src);
	char *strncat(char *dest, const char *src, size_t count);
	
Compare:
	int strcmp(const char *lhs, const char *rhs);
```

使用string class

```
string class provides functions to manipulate and examinate strings.
std::string str1 = "Hello";
std::string str2 = "SUSTech";
std::string result = str1 + "," + str2;
```

```
different types of strings
std::string
std::wstring
std::u8string	//(C++20)
std::u16string	//(C++11)
std::u32string	//(C++11)
```

## 4.2 structures-unions-enumerations

计算机为了存储的方便，它存储变量都是对齐的，在读写的时候效率更高

如下图所示，左面的占12个字节，右面的占16个字节

![](./image/0005.png)



```c++
struct Student{
	int id;
	bool male;
	char label;
	float height;
}
Student stu;
stu.id = 123;

No typedef needed in C++!
```



union

```
union ipv4address{
	std::uint32_t address32;
	std::uint8_t address8[4];
}
sizeof(union ipv4address) is 4;

结构体中的所有成员共享同一个内存，也就是说上面中，那两个成员变量的首地址相同。如果他有多个成员变量，那么以最大的成员变量为准，就是整个union的大小。

```

enum

```
enum color {WHITE,BLACK,RED,GREEN,BLUE,YELLOW,NUM_COLORS};
enum color pen_color = RED;

```

```c++
enum datatype {TYPE_INT8=1,TYPE_INT16=2,TYPE_INT32=4,TYPE_INT64=8}
struct Point{
	enum datatype type;
	union {
		std::int8_t data8[3];
		std::int16_t data16[3];
		std::int32_t data32[3];
		std::int64_t data64[3];
	};
};
size_t datawidth(struct Point pt)
{
	return size_t(pt.type) * 3;
}

int64_t l2norm(struct Point pt)
{
	int64_t result = 0;
	switch(pt.type)
	{
		case (TYPE_INT8):
			result = abs(pt.data8[0])+
					abs(pt.data8[1])+
					abs(pt.data8[2]);
			break;
			....
	}
}
```

```
struct Point point1 = {.type=TYPE_INT8, .data8={-2,3,4}};
```

typedef

```
typedef unsigned char vec3b[3];

unsigned char color[3];
vec3b color = {255,0,255};
```



## 5.1 pointer

```c++
结构体的指针：
struct Student
{
	char name[4];
	int born;
	bool male;
};
Student stu = {"Yu", 2000, true};
Student *pStu = &stu;

p->member;
(*p).member;
```

```c++
Student students[128];
Student * p0 = &students[0];

// the same behavier
students[0].born = 2000;
p0->born = 2000;
```



## 5.2 Constant pointers

```c++
int num = 1;
int another = 2;
//You cannot change the value the p1 points to through p1
const int * p1 = &num;
*p1 = 2;//error
num = 3;//okay

//You cannot change value of p2 (address)
int * const p2 = &num;
*p2 = 3;//okay
p2 = &another;//error

//You cannot change either of them
const int * const p3 = &num;
```

用处

```c++
//当我在一个函数里传入变量的时候，我不希望在函数中去修改这个变量就可以加const
int foo(const char * p)
{
	//the value that p points to cannot be changed
	
	//play a trick?
	char * p2 = p;//syntax error
	//...
	
	return 0;
}
```

## 5.3 pointer-array

```
&students
students
&students[0]
这三个的值是相同的，都是数组的首地址

Students * p = students;
p[0].born = 2001;
可以以这种方式修改值
```



在进行p+1的操作的时候，并不是把p偏移1个字节，而是偏移int个字节，这里的int代表的就是指针的类型int * p

![](./image/0006.png)

```
下面这些是等价的
int i = ...;
int * p = ...;

p[i] = 3;

*(p+i) = 3;

int * p2 = p+i; *p2 = 3;
```

可以把数组和指针很相似，但是数组是constant指针，只能指向那块内存 不能随便乱指，指针可以随便乱指。

区别2就是sizeof的不同，数组的sizeof值为所有值所占的内存，指针的sizeof为存储地址所占的内存

```
int numbers[4] = {0,1,2,3};
int *p = numbers;
cout << sizeof(numbers) << endl;//4*sizeof(int)
cout << sizeof(p) << endl;//4 or 8
cout << sizeof(double *) << endl;//4 or 8
```

## 5.4 allocate memory C

申请内存使用malloc（）

```
int *p = (int *)malloc(4);
理论上来说是申请了4字节的空间，但是系统会分配16字节的空间，是因为要进行对齐，剩余的12字节就算是浪费了
```

释放内存

```
void free(void* ptr);


p = (int *) malloc (sizeof(int));
free(p)
```

```;
浪费情况1：
p = (int *) malloc (sizeof(int)*4);
//...
p = (int *) malloc (sizeof(int)*8);
//...
free(p);
此时释放的内存为第二次申请的内存，第一次申请的内存由于没有被释放且没有指针了，所以也没有办法找到他了
```

```
void foo()
{
	int *p = (int *) malloc (sizeof(int));
	return;
}
当return之后，p为局部变量，return之后，p就消失了，作用域就到此为止了，这段内存也无法回收了，造成了内存泄漏
```

## 5.5 allocate memory C++

申请内存使用new、new[]

```c++
//allocate an int, default initializer (do nothing)
int *p1 = new int;
//allocate an int, initialized to 0
int *p2 = new int();
//allocate an int, initialized to 5
int *p3 = new int(5);
//allocate an int, initialized to 0
int *p4 = new int{};//C++11
//allocate an int, initialized to 5
int *p5 = new int{5};//C++11

//allocate a Student object, default initializer
Student *ps1 = new Student;
//allocate a Student object, initialize the memebers
Student *ps2 = new Student{"Yu", 2020, 1};
```

```c++
//allocate 16 int, default initializer(do nothing)
int *pa1 = new int[16];
//allocate 16 int, zero initialized
int *pa2 = new int[16]();
//allocate 16 int, zero initialized
int *pa3 = new int[16]{};//C++11
//allocate 16 int, the first 3 element are initialized to 1,2,3, the rest 0
int *pa4 = new int[16]{1,2,3};//C++11

Student *psa1 = new Student[16];

Student *psa2 = new Student[16]{{"li",2000,1},{"Yu",2001,1}}
```

释放内存

```c++
//deallocate memory
delete p1;
//deallocate memory
delete ps1;

//deallocate the memory of the array
delete pa1;
//deallocate the memory of the array
delete []pa2;

//deallocate the memory of the array, and call the destructor of the first element
delete psa1;
//deallocate the memory of the array, and call the destructors of all the elements
delete []psa2;
```

**建议在释放数组的时候就统一使用 delete []psa2这种**



## 6.1 function

头文件的知识

![](./image/0007.jpg)

```
#ifndef _DRAW_H_：这里ifdef是一个宏定义，后面的是名字，可以任意指定
在这里应用
#ifndef
#define
...
#endif
这种格式是为了防止重复定义函数
```

```
实参和形参的问题：在6.2节里面讲了
我的理解是：只要碰到一个定义变量的符号就可以认为在内存中找了一块空间来copy存储了另外一份变量，与原来的变量值相同 但是地址不同
```

## 6.2 references：引用

```
可以理解为：为变量起一个别名
int num = 0;
int &num_ref = num;
此时num_ref就可以当作num来看待了，他们在内存中指向的是同一块地址
相当于一个起别名的操作
```

```
需要注意的地方就是：在定义引用的时候就要初始化
int &num_ref;//error
```

```
引用相较于指针来说更加安全
```

```
为了防止在函数中修改引用的变量，可以加const修饰
float matrix_max(const struct Matrix & mat)
```

## 6.3 inline function

```
宏定义虽然灵活但是易于出错，所以在C++中不太鼓励使用宏

#define MAX_MACRO(a,b) (a) > (b) ? (a) : (b)
```

```
inline函数：
在程序中调用函数的话是会有部分内存消耗的，比如会在调用函数之前存储当前的状态，因此如果我们的函数过于简单并且频繁调用的话，还不如不调用；因此，inline函数产生了：
inline float max(float a,float b){}
使用inline的话，会建议编译器使用inline的方式来编译函数，但是也只是个建议，编译器可能不会执行。编译器会根据函数的复杂度来选择，根据整体的情况来选择
```

## 7.1 default arguments

```
argument和parameter的区别：虽然都是参数的意思
float norm(float x, float y, float z = 0);
在这里:0是argument，z是parameter
```

```
float norm(float x, float y, float z)
float norm(float x, float y, float z = 0)
float norm(float x, float y = 0, float z)
在定义默认参数的时候要从尾部开始默认定义，而且可以理解为默认参数是可以叠加的，比如上边第二行定义的z=0，它同样在第三个函数起作用 第三个函数也默认z为0
```

7.1看完了







# 目前还未搞懂的地方

int8_t、int16_t的意思

size_t：表示一个很大的整数？

# 杂

```
宏是不可以换行的

#define .......
可以使用 \ 进行换行
例子：
#define PRINT_ARRAY(array, n) \
for(int idx = 0;idx < (n);idx++) \
	cout << "array[" << idx << "] = " << (array)[idx] << endl;
	
上面(a)加括号的意义就是防止传入一些运算符之类的东西，影响运算级
记住在宏里面参数都加括号就行了
```

