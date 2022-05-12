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

