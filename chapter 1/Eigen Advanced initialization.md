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

类似的，静态方法`Constant(value)`把所有系数都设置为value。如果对象的大小需要指定，除了value参数还需要额外的参数，如 `MatrixXd::Constant(rows, cols, value)`。方法`Random()`用随机的数字填充矩阵或阵列。使用`Identity()`获取单位矩阵，这只能用于matrix而不能用于vector，因为单位矩阵的概念是线性代数中的。方法`LinSpaced(size,low,high)`只对向量和一维阵列有效，它产生一个指定大小的向量，在low和high的等差数列。下面的例子解释`LinSpaced()`，其打印一个表格，表格中是角度和弧度的对应值，还有他们的sin和cos值。

```c++
ArrayXXf table(10, 4);
table.col(0) = ArrayXf::LinSpaced(10, 0, 90);
table.col(1) = M_PI / 180 * table.col(0);
table.col(2) = table.col(1).sin();
table.col(3) = table.col(1).cos();
std::cout << "  Degrees   Radians      Sine    Cosine\n";
std::cout << table << std::endl;
Output:
Degrees   Radians      Sine    Cosine
        0         0         0         1
       10     0.175     0.174     0.985
       20     0.349     0.342      0.94
       30     0.524       0.5     0.866
       40     0.698     0.643     0.766
       50     0.873     0.766     0.643
       60      1.05     0.866       0.5
       70      1.22      0.94     0.342
       80       1.4     0.985     0.174
       90      1.57         1 -4.37e-08
```

# 用作临时对象

如上所示，静态方法`Zero()`和`Constant()` 可以在声明时进行初始化或者在赋值操作符右侧进行初始化。你可以认为这些方法返回一个矩阵或者阵列，实际上，它们返回所谓的表达式对象，该对象当在需要的时候才被计算，所以这样的语法不会带来任何开销。

这些表达式也可以用作临时对象。如下为[Getting started](http://eigen.tuxfamily.org/dox/GettingStarted.html)的第二个例子，其说明了这个特性：

```c++
#include <iostream>
#include <Eigen/Dense>
 
using Eigen::MatrixXd;
using Eigen::VectorXd;
 
int main()
{
  MatrixXd m = MatrixXd::Random(3,3);
  m = (m + MatrixXd::Constant(3,3,1.2)) * 50;
  std::cout << "m =" << std::endl << m << std::endl;
  VectorXd v(3);
  v << 1, 2, 3;
  std::cout << "m * v =" << std::endl << m * v << std::endl;
}

Output:
	
m =
  94 89.8 43.5
49.4  101 86.8
88.3 29.8 37.8
m * v =
404
512
261
```

表达式 `m + MatrixXf::Constant(3,3,1.2)` 构造了一个3*3元素全为1.2的矩阵然后与对应的m元素相加。

逗号初始化也可以构造临时对象，下面的例子构造了一个2*3的随机矩阵，然后与矩阵[0,1;1,0]相乘。

```c++
MatrixXf mat = MatrixXf::Random(2, 3);
std::cout << mat << std::endl << std::endl;
mat = (MatrixXf(2,2) << 0, 1, 1, 0).finished() * mat;
std::cout << mat << std::endl;
Output:
 0.68  0.566  0.823
-0.211  0.597 -0.605

-0.211  0.597 -0.605
  0.68  0.566  0.823
```

在完成临时子矩阵的逗号初始化之后，这里需要使用finished()方法来获得实际的矩阵对象。
