[Eigen: Matrix and vector arithmetic](http://eigen.tuxfamily.org/dox/group__TutorialMatrixArithmetic.html)

该文章主要大体的展示了如何使用Eigen在矩阵、向量、标量之间进行运算。

# 介绍

Eigen通过重载C++运算符号如+、-、*或者一些特殊的方法dot()、cross()等来完成计算操作。对于matrix类（矩阵和向量），相关的操作只支持线性代数的运算。例如，matrix1 * matrix2代表着矩阵的乘法；vector+scalar是不被允许的。如果你想执行各种各样的数组操作，而不是线性代数运算，请查阅[next page](http://eigen.tuxfamily.org/dox/group__TutorialArrayClass.html)。

# 加法和减法

在操作符左右两侧必须有同样的行数和同样的列数。它们的元素必须是同种类型。因为Eigen不支持自动类型转换。

- binary operator + as in `a+b`
- binary operator - as in `a-b`
- unary operator - as in `-a`
- compound operator += as in `a+=b`
- compound operator -= as in `a-=b`

```C++
#include <iostream>
#include <Eigen/Dense>
 
int main()
{
  Eigen::Matrix2d a;
  a << 1, 2,
       3, 4;
  Eigen::MatrixXd b(2,2);
  b << 2, 3,
       1, 4;
  std::cout << "a + b =\n" << a + b << std::endl;
  std::cout << "a - b =\n" << a - b << std::endl;
  std::cout << "Doing a += b;" << std::endl;
  a += b;
  std::cout << "Now a =\n" << a << std::endl;
  Eigen::Vector3d v(1,2,3);
  Eigen::Vector3d w(1,0,0);
  std::cout << "-v + w - v =\n" << -v + w - v << std::endl;
}
Output:
	
a + b =
3 5
4 8
a - b =
-1 -1
 2  0
Doing a += b;
Now a =
3 5
4 8
-v + w - v =
-1
-4
-6
```

# 标量的乘除

乘法和除法是非常简单的

- binary operator * as in `matrix*scalar`
- binary operator * as in `scalar*matrix`
- binary operator / as in `matrix/scalar`
- compound operator *= as in `matrix*=scalar`
- compound operator /= as in `matrix/=scalar`

```C++
#include <iostream>
#include <Eigen/Dense>
 
int main()
{
  Eigen::Matrix2d a;
  a << 1, 2,
       3, 4;
  Eigen::Vector3d v(1,2,3);
  std::cout << "a * 2.5 =\n" << a * 2.5 << std::endl;
  std::cout << "0.1 * v =\n" << 0.1 * v << std::endl;
  std::cout << "Doing v *= 2;" << std::endl;
  v *= 2;
  std::cout << "Now v =\n" << v << std::endl;
}
Output:
a * 2.5 =
2.5   5
7.5  10
0.1 * v =
0.1
0.2
0.3
Doing v *= 2;
Now v =
2
4
6
```

# 表达式模板的注解

这是一个比较高级的主题在这篇文章中，但是，现在提出是比较有用的。在Eigen，算数操作，例如+，他们自己不执行任何操作，他们只是返回一个表达式对象，该对象描述了将要执行的计算。实际的计算发生在后面，当整个表达式被求值时，通常是使用=。虽然这听起来很繁重，但任何现代优化编译器都能够优化掉这种抽象，从而得到完美优化的代码。例如，当你这样做时：

```c++
VectorXf a(50), b(50), c(50), d(50);
...
a = 3*b + 4*c + 5*d;
```

Eigen会把上述代表编译成一个循环，这个数组只遍历一次。这个数组循环看起来像这样：

```C++
for(int i = 0; i < 50; ++i)
  a[i] = 3*b[i] + 4*c[i] + 5*d[i];
```

因此，你不要害怕使用相对较大的运算表达式，这只会给Eigen更多机会进行优化。

# Transposition and conjugation























