[Eigen: The Matrix class](http://eigen.tuxfamily.org/dox/group__TutorialMatrixClass.html)



在Eigen中，所有的矩阵和向量都是Matrix类模板的对象。向量只是行数或者列数为一的特殊矩阵。

# Matrix前三个模板参数

Matrix类有六个模板参数，但是目前为止学习前三个参数即可，剩下的三个都有默认的参数值，我们以后再讨论后面的三个。

Matrix类三个强制性的模板参数是：

```C++
Matrix<typename Scalar, int RowsAtCompileTime, int ColsAtCompileTime>
```

- Scalar是标量的类型，例如系数的类型。如果你想要一个float类型的矩阵，那么你可以再次填写float。点击[Scalar types](http://eigen.tuxfamily.org/dox/TopicScalarTypes.html)查看标量的类型和如何添加新的类型。
- RowsAtCompileTime和ColsAtCompileTime是矩阵的行和列数，这是在编译期间就知道的。点击[below](http://eigen.tuxfamily.org/dox/group__TutorialMatrixClass.html#TutorialMatrixDynamic)查看，如果不知道矩阵的大小该如何做。

我们提供了很多通常使用的类型。例如，Matrix4f是4*4的float矩阵，这在Eigen中定义为：

```C++
typedef Matrix<float, 4, 4> Matrix4f;
```

# 向量

如上面所提到的，在Eigen中，向量仅仅是一种特殊的矩阵，把只有一列的矩阵叫做列向量，经常把列向量称为向量。行数为一的叫做行向量。

例如，Vector3f是3个float的列向量。这在Eigen中这样定义：

```c++
typedef Matrix<int, 1, 2> RowVector2i;
```

# 动态的特殊值

当然，Eigen不限于指定大小的矩阵，RowsAtCompileTime和ColsAtCompileTime模板参数可以是一个动态值，这表明在编译阶段这个大小是不知道的，因为必须当作运行时的变量进行处理。在Eigen的俗术语中，这叫做动态大小；如果在编译期间就知道大小的叫做固定大小。例如，MatrixXd是double类型的动态大小：

```C++
typedef Matrix<double, Dynamic, Dynamic> MatrixXd;
```

类似的，我们定义VectorXi：

```c++
typedef Matrix<int, Dynamic, 1> VectorXi;
```

您可以使用一个固定的行数和一个动态的列数

```c++
Matrix<float, 3, Dynamic>
```

# 构造函数

默认的构造函数总是可以使用的，它从来不执行任何动态内存分配，也从来不初始化矩阵元素。你可以这么做：

```c++
Matrix3f a;
MatrixXf b;
```

- a是一个3*3的矩阵，他是一个9个大小的数组，而且没有任何初始化。
- b是一个动态大小的矩阵，它的大小目前是0*0，它的内存也没有开辟

也有指定大小的构造函数。对于矩阵来说，第一个参数总是行，对于向量来说，只需要传入向量的大小即可。构造函数按照给定的大小开辟内存，但是并没有初始化内存。

```C++
MatrixXf a(10,15);
VectorXf b(30);
```

- a是一个10*15的动态大小的矩阵，开辟了内存但是没有初始化
- b是一个动态大小为30的向量，同样开辟了内存但是没有初始化

为了提供动态大小和固定大小矩阵统一的API，您可以向固定大小矩阵的构造函数传递大小，尽管这是没有用的，但是是合法的：

```C++
Matrix3f a(3,3);
```

这并没有任何操作。

矩阵和向量也可以从一个列表及进行初始化。在C++11之前，这个功能这个只能用在固定大小的列或者向量并且其大小要小于4。

```C++
Vector2d a(5.0, 6.0);
Vector3d b(5.0, 6.0, 7.0);
Vector4d c(5.0, 6.0, 7.0, 8.0);
```

如果使用C++11编译，任意固定大小的行或者列向量都可以使用这种方式传递向量。

```C++
Vector2i a(1, 2);                      // A column vector containing the elements {1, 2}
Matrix<int, 5, 1> b {1, 2, 3, 4, 5};   // A row-vector containing the elements {1, 2, 3, 4, 5}
Matrix<int, 1, 5> c = {1, 2, 3, 4, 5}; // A column vector containing the elements {1, 2, 3, 4, 5}
```

通常，固定大小或者运行时大小固定的矩阵和向量，元素的系数是按照行进行分组的，然后使用该行去初始化矩阵的一行。

```C++
MatrixXi a {      // construct a 2x2 matrix
      {1, 2},     // first row
      {3, 4}      // second row
};
Matrix<double, 2, 3> b {
      {2, 3, 4},
      {5, 6, 7},
};
```

对于列或者行向量，隐士的转置是被允许的。这意味着一个列向量可以被一个行进行初始化。

```C++
VectorXd a {{1.5, 2.5, 3.5}};             // A column-vector with 3 coefficients，隐式转换了
RowVectorXd b {{1.0, 2.0, 3.0, 4.0}};     // A row-vector with 4 coefficients
```

# 访问系数

在Eigen中主要的系数访问方法是重载括号运算符。对于矩阵，行索引总是优先传递的。对于向量，只需要传递一个索引。索引从0开始。下面例子很清楚的解释了这一点。

```C++
#include <iostream>
#include <Eigen/Dense>
 
int main()
{
  Eigen::MatrixXd m(2,2);
  m(0,0) = 3;
  m(1,0) = 2.5;
  m(0,1) = -1;
  m(1,1) = m(1,0) + m(0,1);
  std::cout << "Here is the matrix m:\n" << m << std::endl;
  Eigen::VectorXd v(2);
  v(0) = 4;
  v(1) = v(0) - 1;
  std::cout << "Here is the vector v:\n" << v << std::endl;
}

Here is the matrix m:
  3  -1
2.5 1.5
Here is the vector v:
4
3
```

请注意m(index)不只是只能用在向量，对普通的矩阵同样可以使用，在系数行列式中点访问是基于索引的。但是，索引取决于矩阵的存储顺序。在Eigen中，矩阵默认是列优先进行存储，但是这也可以改变，详情请查阅 [Storage orders](http://eigen.tuxfamily.org/dox/group__TopicStorageOrders.html)。

运算符**[ ]**同样也被重载用于基于索引的向量访问，但是请记住，C++只允许远算符**[ ]**最多传入1个参数。我们限制了运算符**[ ]**只能在vector上的使用，因为C++语言的笨拙，会把matrix[i,j]编译成matrix[j]。

# 逗号初始化

矩阵和向量系数可以使用逗号进行初始化。如下：

```C++
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

# 重置大小

矩阵当前的大小可以通过函数rows(),cols(),size()来返回。这些方法分别返回函数、列数、系数的数量。对于动态大小的矩阵，可以使用函数resize()来重新调整大小。

```C++
#include <iostream>
#include <Eigen/Dense>
 
int main()
{
  Eigen::MatrixXd m(2,5);
  m.resize(4,3);
  std::cout << "The matrix m is of size "
            << m.rows() << "x" << m.cols() << std::endl;
  std::cout << "It has " << m.size() << " coefficients" << std::endl;
  Eigen::VectorXd v(2);
  v.resize(5);
  std::cout << "The vector v is of size " << v.size() << std::endl;
  std::cout << "As a matrix, v is of size "
            << v.rows() << "x" << v.cols() << std::endl;
}

Output:
The matrix m is of size 4x3
It has 12 coefficients
The vector v is of size 5
As a matrix, v is of size 5x1
```

如果矩阵的大小没有改变，那么resize()方法不做任何操作。否则，该返回会破坏当前的矩阵，矩阵的系数可能会改变。如果你不想改变矩阵的系数，可以使用resize()的变体[conservativeResize()](http://eigen.tuxfamily.org/dox/classEigen_1_1PlainObjectBase.html#a712c25be1652e5a64a00f28c8ed11462)。

所有的这些方法都可以对固定大小的矩阵使用。当然，你不能对一个固定大小的矩阵使用resize。试图对一个固定大小的矩阵进行改变大小会触发断言。但是下列的代码是合法的。

```C++
#include <iostream>
#include <Eigen/Dense>
 
int main()
{
  Eigen::Matrix4d m;
  m.resize(4,4); // no operation
  std::cout << "The matrix m is of size "
            << m.rows() << "x" << m.cols() << std::endl;
}
Output:
The matrix m is of size 4x4
```

# 赋值和重置大小

赋值是使用操作符=来完成复制的操作。Eigen将会自动调整等式左边的大小以便与和等式右边的大小做一个匹配，例如：

```C++
MatrixXf a(2,2);
std::cout << "a is of size " << a.rows() << "x" << a.cols() << std::endl;
MatrixXf b(3,3);
a = b;
std::cout << "a is now of size " << a.rows() << "x" << a.cols() << std::endl;
Output:
a is of size 2x2
a is now of size 3x3
```

当然如果，等式左边的大小是固定大小，重置大小是不被允许的。

如果你不想自动重置大小的操作发生，你可以关闭它，详情请参考[this page](http://eigen.tuxfamily.org/dox/TopicResizing.html)。

# 固定和动态大小的比较

如何选择使用固定大小还是动态大小呢？对于非常小的矩阵应该选择固定大小，当面对大矩阵的时候应该使用动态大小。对于小的大小，特别是对于大小小于16时，使用固定大小效率更高，因为它允许Eigen避免动态分配内存和展开循环。一个固定大小Eigen矩阵仅仅是一个纯数组。例如，`Matrix4f mymatrix`等同于`float mymatrix[16]`，所以这没有没有运行的成本。而动态矩阵需要在堆中分配空间，例如：`MatrixXf mymatrix(rows,columns)`等价于`float *mymatrix = new float[rows*columns]`，除此之外，MatrixXf对象还存储了其行数和列数。

使用固定大小的限制就是在要求你在编译的时候就知道大小，而且对于大的矩阵，例如大于32的矩阵，使用固定大小的矩阵带来的优势可以忽略不计。更糟糕的是，试图使用固定大小的矩阵创建一个非常大的矩阵可能会导致栈溢出，因为Eigen使用分配一个很大的栈空间来存储这个局部变量。最后，看情况，如果使用动态大小，Eigen可以更加积极的去向量化操作，具体请查看 [Vectorization](http://eigen.tuxfamily.org/dox/TopicVectorization.html)。

# 可选择的其他模板参数

在开始的时候，我们提及Matrix类有6个模板参数，但是，目前我们只讨论了前三个，剩下的三个参数是可供选择的。下面是完整的模板参数：

```C++
Matrix<typename Scalar,
       int RowsAtCompileTime,
       int ColsAtCompileTime,
       int Options = 0,
       int MaxRowsAtCompileTime = RowsAtCompileTime,
       int MaxColsAtCompileTime = ColsAtCompileTime>
```

- Options 是一个位域。这里，我们只讨论一个位RowMajor。它指定了矩阵是按照行优先进行存储的。默认情况下，存储顺序是按照列存储的。例如```Matrix<float, 3, 3, RowMajor>```是指一个行优先3*3矩阵。
- MaxRowsAtCompileTime 和 MaxColsAtCompileTime 是矩阵大小的上界，这让在编译前不知道矩阵的具体大小，就可以分配最大多少的内存，例如，```Matrix<float, Dynamic, Dynamic, 0, 3, 4>``` 会在编译的时候就使用一个大小为12个float的纯数组，而不需要动态分配内存。

# 常用的类型

- MatrixNt **<=>** Matrix<type, N, N>. **例如** Matrix<int, Dynamic, Dynamic>.
- MatrixXNt  **<=>** Matrix<type, Dynamic, N>.**例如**Matrix<int, Dynamic, 3>.
- MatrixNXt **<=>** Matrix<type, N, Dynamic>. **例如** Matrix<d, 4, Dynamic>.
- VectorNt  **<=>** Matrix<type, N, 1>. **例如** Vector2f for Matrix<float, 2, 1>.
- RowVectorNt  **<=>** Matrix<type, 1, N>.  **例如** Matrix<double, 1, 3>.

上述：

- **N**代表2、3、4或者X
- **t**代表任何一个i(int)、f(float)、d(double)、cf(complex<float>)、cd(complex<double>)。事实上，typedefs只定义了5种类型，但是这并不意味着，他们仅仅支持标量。例如，标准整形是支持的。



























