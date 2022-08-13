[Eigen: Reshape](http://eigen.tuxfamily.org/dox/group__TutorialReshape.html)

自从3.4版本以后，Eigen提供了可以对矩阵或向量重新塑形更方便的方法，所有的操作可以通过`DenseBase::reshaped(NRowsType,NColsType)`和`DenseBase::reshaped()`两个函数完成。该类函数并不改变原有的变量，而是返回一个重新塑形后的版本。

# 二维度reshape

通常的reshape操作是通过`reshaped(nrows,ncols)`来完成，下面是一个例子把4\*4的矩阵变成2\*8的矩阵。

```c++
Matrix4i m = Matrix4i::Random();
cout << "Here is the matrix m:" << endl << m << endl;
cout << "Here is m.reshaped(2, 8):" << endl << m.reshaped(2, 8) << endl;

Output:
	
Here is the matrix m:
 7  9 -5 -3
-2 -6  1  0
 6 -3  0  9
 6  6  3  9
Here is m.reshaped(2, 8):
 7  6  9 -3 -5  0 -3  9
-2  6 -6  6  1  3  0  9
```

不管输入表达式的存储顺序，默认情况下，所有的系数都按照列来排序。想要控制顺序、编译时的大小和自动约简，可以参考`DenseBase::reshaped(NRowsType,NColsType)`的文档，文档中有很多细节。

# 一维reshape

一个常见的用法即把二维的矩阵或者表达式变成一维的形式。这种情况下，维度可以计算出来，所以就不用传入相关参数了，如下：

```c++
Matrix4i m = Matrix4i::Random();
cout << "Here is the matrix m:" << endl << m << endl;
cout << "Here is m.reshaped().transpose():" << endl << m.reshaped().transpose() << endl;
cout << "Here is m.reshaped<RowMajor>().transpose():  " << endl << m.reshaped<RowMajor>().transpose() << endl;
Output:
Here is the matrix m:
 7  9 -5 -3
-2 -6  1  0
 6 -3  0  9
 6  6  3  9
Here is m.reshaped().transpose():
 7 -2  6  6  9 -6 -3  6 -5  1  0  3 -3  0  9  9
Here is m.reshaped<RowMajor>().transpose():  
 7  9 -5 -3 -2 -6  1  0  6 -3  0  9  6  6  3  9
```

该方法返回列向量，并且，默认情况下输入系数总是按列排序。如果想要改变可以参考` DenseBase::reshaped() `相关文档。

# 原地reshape

上述的例子都是返回另一个表达式，而不是原地修改变量，那么如何原地修改变量的大小呢？当然，这只对具有动态大小的矩阵或者阵列有用。大多数情况下，可以使用[PlainObjectBase::resize(Index,Index)](http://eigen.tuxfamily.org/dox/classEigen_1_1PlainObjectBase.html#a9fd0703bd7bfe89d6dc80e2ce87c312a)

```c++
MatrixXi m = Matrix4i::Random();
cout << "Here is the matrix m:" << endl << m << endl;
cout << "Here is m.reshaped(2, 8):" << endl << m.reshaped(2, 8) << endl;
m.resize(2,8);
cout << "Here is the matrix m after m.resize(2,8):" << endl << m << endl;
Output:
Here is the matrix m:
 7  9 -5 -3
-2 -6  1  0
 6 -3  0  9
 6  6  3  9
Here is m.reshaped(2, 8):
 7  6  9 -3 -5  0 -3  9
-2  6 -6  6  1  3  0  9
Here is the matrix m after m.resize(2,8):
 7  6  9 -3 -5  0 -3  9
-2  6 -6  6  1  3  0  9
```

但是，需要注意的是不像reshape，resize的顺序取决与输入的存储顺序，因此和`reshaped<AutoOrder>`表现类似。

```c++
Matrix<int,Dynamic,Dynamic,RowMajor> m = Matrix4i::Random();
cout << "Here is the matrix m:" << endl << m << endl;
cout << "Here is m.reshaped(2, 8):" << endl << m.reshaped(2, 8) << endl;
cout << "Here is m.reshaped<AutoOrder>(2, 8):" << endl << m.reshaped<AutoOrder>(2, 8) << endl;
m.resize(2,8);
cout << "Here is the matrix m after m.resize(2,8):" << endl << m << endl;
Output:
Here is the matrix m:
 7 -2  6  6
 9 -6 -3  6
-5  1  0  3
-3  0  9  9
Here is m.reshaped(2, 8):
 7 -5 -2  1  6  0  6  3
 9 -3 -6  0 -3  9  6  9
Here is m.reshaped<AutoOrder>(2, 8):
 7 -2  6  6  9 -6 -3  6
-5  1  0  3 -3  0  9  9
Here is the matrix m after m.resize(2,8):
 7 -2  6  6  9 -6 -3  6
-5  1  0  3 -3  0  9  9
```

最后，给自身赋一个reshape的值是禁止的，这是因为这样会由于混淆而导致未定义行为。`A  = A.reshaped(2,8)`是禁止的，而`A = A.reshaped(2,8).eval(); `是可以的。
