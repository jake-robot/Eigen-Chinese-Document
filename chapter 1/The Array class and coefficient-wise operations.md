[Eigen: The Array class and coefficient-wise operations](http://eigen.tuxfamily.org/dox/group__TutorialArrayClass.html)

该文章主要是介绍Eigen的数组类

# 什么是数组类？

与Matrix类用于线性代数计算不同的是，Array类提供了通用目的数组。更进一步的说，Array类提供了更简单的方式去操作按照系数，这可能没有线性代数的意义，例如对每一个元素都相加一个常数或者对应元素相乘

# 数组类型

Array和Matrix类都是类模板。与Matrix一样，Array的前三个参数是强制性的：

```c++
Array<typename Scalar, int RowsAtCompileTime, int ColsAtCompileTime>
```

后三个参数是可选择的。因为都是一样的，所以，再次我们不解释了。

对于一些常见的情况，Eigen同时也提供了已经定义好的类型，这与Matrix类相同，但是还有有一点区别的。array用在一维和二维数组上。我们使用ArrayNt代表一维N个大小的标量；使用ArrayNNt代表二维，解释如下：

| Type                          | Typedef  |
| :---------------------------- | :------- |
| Array<float,Dynamic,1>        | ArrayXf  |
| Array<float,3,1>              | Array3f  |
| Array<double,Dynamic,Dynamic> | ArrayXXd |
| Array<double,3,3>             | Array33d |

# 访问数组内部的值

与Matrices相同，重载了括号运算符来读写数组元素的系数，使用`<<`来初始化数组或者打印数据。

```c++
#include <Eigen/Dense>
#include <iostream>
 
int main()
{
  Eigen::ArrayXXf  m(2,2);
  
  // assign some values coefficient by coefficient
  m(0,0) = 1.0; m(0,1) = 2.0;
  m(1,0) = 3.0; m(1,1) = m(0,1) + m(1,0);
  
  // print values to standard output
  std::cout << m << std::endl << std::endl;
 
  // using the comma-initializer is also allowed
  m << 1.0,2.0,
       3.0,4.0;
     
  // print values to standard output
  std::cout << m << std::endl;
}
Output:
1 2
3 5

1 2
3 4
```

# 加法和减法

加法和减法与matrices相同，只有数组的大小相同才可以，它们是对应元素相加或者相减。

数组同时支持`array + scalar`的表达形式，这对数组的每一个系数都相加一个常数。这是在Matrix类中所不可以实现的。

```c++
#include <Eigen/Dense>
#include <iostream>
 
int main()
{
  Eigen::ArrayXXf a(3,3);
  Eigen::ArrayXXf b(3,3);
  a << 1,2,3,
       4,5,6,
       7,8,9;
  b << 1,2,3,
       1,2,3,
       1,2,3;
       
  // Adding two arrays
  std::cout << "a + b = " << std::endl << a + b << std::endl << std::endl;
 
  // Subtracting a scalar from an array
  std::cout << "a - 2 = " << std::endl << a - 2 << std::endl;
}
Output:
a + b = 
 2  4  6
 5  7  9
 8 10 12

a - 2 = 
-1  0  1
 2  3  4
 5  6  7
```

# 数组乘法

首先，你可以使用一个数组乘以一个标量来完成，这和matrices一样，但是当你把两个Array相乘的时候，Array会对应元素相乘，但是matrices确是矩阵的乘法。所以Array相乘的时候，其大小必须是一样的。

```c++
#include <Eigen/Dense>
#include <iostream>
 
int main()
{
  Eigen::ArrayXXf a(2,2);
  Eigen::ArrayXXf b(2,2);
  a << 1,2,
       3,4;
  b << 5,6,
       7,8;
  std::cout << "a * b = " << std::endl << a * b << std::endl;
}
Output:
a * b = 
 5 12
21 32
```

# 其他按系数操作

除了加减乘除，Array还定义了其他按系数操作的运算，例如`.abs()`对每一个元素求绝对值`.sqrt()` 计算每个元素的平方根。如果你有两个同样大小的array，你可以通过`.min(.)`来构造一个array，其每个元素的值是两个array中的较小的一个。示例如下：

```c++
#include <Eigen/Dense>
#include <iostream>
 
int main()
{
  Eigen::ArrayXf a = Eigen::ArrayXf::Random(5);
  a *= 2;
  std::cout << "a =" << std::endl
            << a << std::endl;
  std::cout << "a.abs() =" << std::endl
            << a.abs() << std::endl;
  std::cout << "a.abs().sqrt() =" << std::endl
            << a.abs().sqrt() << std::endl;
  std::cout << "a.min(a.abs().sqrt()) =" << std::endl
            << a.min(a.abs().sqrt()) << std::endl;
}
Output:
a =
  1.36
-0.422
  1.13
  1.19
  1.65
a.abs() =
 1.36
0.422
 1.13
 1.19
 1.65
a.abs().sqrt() =
1.17
0.65
1.06
1.09
1.28
a.min(a.abs().sqrt()) =
  1.17
-0.422
  1.06
  1.09
  1.28
```

# Array和Matrix表达式之间的转换

如何选择是使用array还是matrix呢？它们之间的运算是不相同的，不能混合使用。因此，当需要线性代数运算时使用matrix，当需要按元素操作时使用array。但是，当你同时需要两个操作的时候这不是很方便，所以你需要两个之间的转换，这就可以同时获得两个所有的运算能力。

Matrix表达式有`.array()`方法可以把matrix转换为array表达式；array()有`.matrix()`方法转换回去。由于Eigen表达式的抽象，这些转换发生在编译的时候，所以不需要任何运行时间成本。`.array()`和`.matrix()`既可以作为左值，也可以作为右值。

但是在Eigen的表达式中混合使用array和matrices是不可取的的。例如，你不可以把一个matrix和array直接相加。但是，你可以转换过后再相加，有一个是例外的，赋值运算符是可以的，可以使用array赋值给matrices，反之亦然。

下面的例子，展示了如何再matrix上使用array的方法。例如，`result = m.array()*m.array()`把两个matrices转换为array，然后使用按元素相乘的方法把结果放在result中。

事实上，在Eigen中这样的用法很常见，所以提供了`const.cwiseProduct(.)`方法来满足matrices的按元素相乘的需要，下面是一个例子：

```c++
#include <Eigen/Dense>
#include <iostream>
 
using Eigen::MatrixXf;
 
int main()
{
  MatrixXf m(2,2);
  MatrixXf n(2,2);
  MatrixXf result(2,2);
 
  m << 1,2,
       3,4;
  n << 5,6,
       7,8;
 
  result = m * n;
  std::cout << "-- Matrix m*n: --\n" << result << "\n\n";
  result = m.array() * n.array();
  std::cout << "-- Array m*n: --\n" << result << "\n\n";
  result = m.cwiseProduct(n);
  std::cout << "-- With cwiseProduct: --\n" << result << "\n\n";
  result = m.array() + 4;
  std::cout << "-- Array m + 4: --\n" << result << "\n\n";
}
Output:
#include <Eigen/Dense>
#include <iostream>
 
using Eigen::MatrixXf;
 
int main()
{
  MatrixXf m(2,2);
  MatrixXf n(2,2);
  MatrixXf result(2,2);
 
  m << 1,2,
       3,4;
  n << 5,6,
       7,8;
 
  result = m * n;
  std::cout << "-- Matrix m*n: --\n" << result << "\n\n";
  result = m.array() * n.array();
  std::cout << "-- Array m*n: --\n" << result << "\n\n";
  result = m.cwiseProduct(n);
  std::cout << "-- With cwiseProduct: --\n" << result << "\n\n";
  result = m.array() + 4;
  std::cout << "-- Array m + 4: --\n" << result << "\n\n";
}
```

类似的，如果array1和array2是数组，`array1.matrix() * array2.matrix()`计算他们的矩阵乘积。

下面是一个更复杂点的例子，表达式`(m.array() + 4).matrix() * m`对每一个元素都相加了4，然后计算m和其结果的矩阵乘积。类似的，表达式`(m.array() * n.array()).matrix() * m`按元素计算矩阵m和n的乘积，然后计算其结果与m的矩阵乘法。

```c++
#include <Eigen/Dense>
#include <iostream>
 
using Eigen::MatrixXf;
 
int main()
{
  MatrixXf m(2,2);
  MatrixXf n(2,2);
  MatrixXf result(2,2);
 
  m << 1,2,
       3,4;
  n << 5,6,
       7,8;
  
  result = (m.array() + 4).matrix() * m;
  std::cout << "-- Combination 1: --\n" << result << "\n\n";
  result = (m.array() * n.array()).matrix() * m;
  std::cout << "-- Combination 2: --\n" << result << "\n\n";
}

Output:
-- Combination 1: --
23 34
31 46

-- Combination 2: --
 41  58
117 170
```

