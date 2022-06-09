[Eigen: Advanced initialization](http://eigen.tuxfamily.org/dox/group__TutorialAdvancedInitialization.html)

本文讨论几个高级的初始化方法，进一步的讨论之前的逗号初始化。同时解释了如何获取特殊的矩阵如单位矩阵和零矩阵。

# 逗号初始化

Eigen提供一个逗号初始化语法，这让用户可以很容易的设置矩阵、向量、阵列的系数。仅仅需要简单的列出系数，从左上角开始，从左向右，从上向下一次列出系数。初始化对象的大小需要提前指定。如果给的系数多了或少了，Eigen会报错。

```c++
Matrix3f m;
m << 1, 2, 3,
     4, 5, 6,
     7, 8, 9;
std::cout << m;
Output:
1 2 3
4 5 6
7 8 9
```

初始化列表它们自身可能是向量或者矩阵。逗号的使用就是把向量和矩阵连接起来。例如，下面是在指定向量大小后，连接两行向量。

```c++
RowVectorXd vec1(3);
vec1 << 1, 2, 3;
std::cout << "vec1 = " << vec1 << std::endl;
 
RowVectorXd vec2(4);
vec2 << 1, 4, 9, 16;
std::cout << "vec2 = " << vec2 << std::endl;
 
RowVectorXd joined(7);
joined << vec1, vec2;
std::cout << "joined = " << joined << std::endl;
Output:
vec1 = 1 2 3
vec2 =  1  4  9 16
joined =  1  2  3  1  4  9 16
```

同样，我们也可以使用同样的技术去以一个块去初始化矩阵。

```c++
MatrixXf matA(2, 2);
matA << 1, 2, 3, 4;
MatrixXf matB(4, 4);
matB << matA, matA/10, matA/10, matA;
std::cout << matB << std::endl;
Output:
 1   2 0.1 0.2
  3   4 0.3 0.4
0.1 0.2   1   2
0.3 0.4   3   4
```

逗号初始化同样也可以填充块表达式如`m.row(i)`，下面是一个更加复杂的方式去实现第一个例子。

```c++
Matrix3f m;
m.row(0) << 1, 2, 3;
m.block(1,0,2,2) << 4, 5, 7, 8;
m.col(2).tail(2) << 6, 9;                   
std::cout << m;

Output:
1 2 3
4 5 6
7 8 9
```

# 特殊的矩阵和阵列

matrix和array类有静态方法如`Zero()`，这可以初始化所有系数为零。其中有三个变体。第一个变体不需要任何参数，仅仅可以用在固定大小的对象。如果你想要一个动态大小的对象，你需要指定大小。因此，第二个变体需要一个参数用来初始化一维动态对象。第三个变体，需要两个参数用来初始化二维对象的大小。所有的变体解释如下：

```c++
std::cout << "A fixed-size array:\n";
Array33f a1 = Array33f::Zero();
std::cout << a1 << "\n\n";
 
 
std::cout << "A one-dimensional dynamic-size array:\n";
ArrayXf a2 = ArrayXf::Zero(3);
std::cout << a2 << "\n\n";
 
 
std::cout << "A two-dimensional dynamic-size array:\n";
ArrayXXf a3 = ArrayXXf::Zero(3, 4);
std::cout << a3 << "\n";

Output:
A fixed-size array:
0 0 0
0 0 0
0 0 0

A one-dimensional dynamic-size array:
0
0
0

A two-dimensional dynamic-size array:
0 0 0 0
0 0 0 0
0 0 0 0
```

