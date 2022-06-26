[Eigen: Reductions, visitors and broadcasting](http://eigen.tuxfamily.org/dox/group__TutorialReductionsVisitorsBroadcasting.html)

本文解释Eigen的约简、访客和广播以及它们如何用于矩阵和数组。

# 约简

在Eigen，约简单是把一个矩阵和数组变成一个标量。一个经常用到的约简是`sum()`,它返回所有矩阵和阵列元素的和。

```c++
#include <iostream>
#include <Eigen/Dense>
 
using namespace std;
int main()
{
  Eigen::Matrix2d mat;
  mat << 1, 2,
         3, 4;
  cout << "Here is mat.sum():       " << mat.sum()       << endl;
  cout << "Here is mat.prod():      " << mat.prod()      << endl;
  cout << "Here is mat.mean():      " << mat.mean()      << endl;
  cout << "Here is mat.minCoeff():  " << mat.minCoeff()  << endl;
  cout << "Here is mat.maxCoeff():  " << mat.maxCoeff()  << endl;
  cout << "Here is mat.trace():     " << mat.trace()     << endl;
}

Output:
Here is mat.sum():       10
Here is mat.prod():      24
Here is mat.mean():      2.5
Here is mat.minCoeff():  1
Here is mat.maxCoeff():  4
Here is mat.trace():     5
```

矩阵的迹可以通过`trace()`来得到，也可以通过`a.diagonal().sum()`得到。

# 模的计算

向量的二范数可以通过[squaredNorm()](http://eigen.tuxfamily.org/dox/classEigen_1_1MatrixBase.html#ac8da566526419f9742a6c471bbd87e0a)来计算，这等价于自己和自己点乘，也等于它系数绝对值的平方和。

Eigen同时提供`norm()`方法，他返回 [squaredNorm()](http://eigen.tuxfamily.org/dox/classEigen_1_1MatrixBase.html#ac8da566526419f9742a6c471bbd87e0a)的平方根。

这些操作同时可以在矩阵上进行操作，在这样的情况下，一个n*p的矩阵被认为是（n\*p）大小的向量，所以对于`norm()`方法，其返回“Frobenius" or "Hilbert-Schmidt" 范数。我们避免谈论矩阵的ℓ2范数，因为那可能意味着不同的事情。

如果你想其它按照系数的范数，可以使用[lpNorm<p>()](http://eigen.tuxfamily.org/dox/classEigen_1_1MatrixBase.html#a72586ab059e889e7d2894ff227747e35)方法，参数p可以使用特殊值如果你想计算无穷范数，其返回矩阵元素的最大值。

```c++
#include <Eigen/Dense>
#include <iostream>
 
int main()
{
  Eigen::VectorXf v(2);
  Eigen::MatrixXf m(2,2), n(2,2);
  
  v << -1,
       2;
  
  m << 1,-2,
       -3,4;
 
  std::cout << "v.squaredNorm() = " << v.squaredNorm() << std::endl;
  std::cout << "v.norm() = " << v.norm() << std::endl;
  std::cout << "v.lpNorm<1>() = " << v.lpNorm<1>() << std::endl;
  std::cout << "v.lpNorm<Infinity>() = " << v.lpNorm<Eigen::Infinity>() << std::endl;
 
  std::cout << std::endl;
  std::cout << "m.squaredNorm() = " << m.squaredNorm() << std::endl;
  std::cout << "m.norm() = " << m.norm() << std::endl;
  std::cout << "m.lpNorm<1>() = " << m.lpNorm<1>() << std::endl;
  std::cout << "m.lpNorm<Infinity>() = " << m.lpNorm<Eigen::Infinity>() << std::endl;
}
Output:
v.squaredNorm() = 5
v.norm() = 2.23607
v.lpNorm<1>() = 3
v.lpNorm<Infinity>() = 2

m.squaredNorm() = 30
m.norm() = 5.47723
m.lpNorm<1>() = 10
m.lpNorm<Infinity>() = 4
```

算子范数：一范数和无穷范数可以很容易的计算，如下：

```c++
#include <Eigen/Dense>
#include <iostream>
 
int main()
{
  Eigen::MatrixXf m(2,2);
  m << 1,-2,
       -3,4;
 
  std::cout << "1-norm(m)     = " << m.cwiseAbs().colwise().sum().maxCoeff()
            << " == "             << m.colwise().lpNorm<1>().maxCoeff() << std::endl;
 
  std::cout << "infty-norm(m) = " << m.cwiseAbs().rowwise().sum().maxCoeff()
            << " == "             << m.rowwise().lpNorm<1>().maxCoeff() << std::endl;
}
Output:
1-norm(m)     = 6 == 6
infty-norm(m) = 7 == 7
```

关于这些表达式的语法具体解释如下。

# 布尔约简

