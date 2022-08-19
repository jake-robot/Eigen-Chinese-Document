[Eigen: STL iterators and algorithms](http://eigen.tuxfamily.org/dox/group__TutorialSTL.html)

自从3\.4版本后，Eigen的稠密矩阵和阵列提供了与STL兼容的迭代器。如下所示，这让他们很自然的兼容范围的for循环和STL算法库。

# 对一维阵列和向量进行迭代

任何有`begin()`和`end()`方法的一维表达式都可以进行迭代。

这直接的让C++11的range循环可以使用：

```c++
VectorXi v = VectorXi::Random(4);
cout << "Here is the vector v:\n";
for(auto x : v) cout << x << " ";
cout << "\n";
Output:
Here is the vector v:
7 -2 6 6 
```

一维表达式同样可以很容易的传递给STL算法：

```c++
Array4i v = Array4i::Random().abs();
cout << "Here is the initial vector v:\n" << v.transpose() << "\n";
std::sort(v.begin(), v.end());
cout << "Here is the sorted vector v:\n" << v.transpose() << "\n";
Output:
Here is the initial vector v:
7 2 6 6
Here is the sorted vector v:
2 6 6 7
```

类似于`std::vector`，一维表达式同样有`cbegin()/cend()`方法去方便的在non-const对象上获得const类型的迭代器。

# 在二维阵列和矩阵上迭代

STL迭代器本质上设计来迭代一维数据结构的。这就是为什么`begin()/end()`方法不可以在二维表达式上使用。迭代二维表达式可以通过reshaped()方法创建一维线性表达式来轻松完成。

```c++
Matrix2i A = Matrix2i::Random();
cout << "Here are the coeffs of the 2x2 matrix A:\n";
for(auto x : A.reshaped())
  cout << x << " ";
cout << "\n";
Output:
Here are the coeffs of the 2x2 matrix A:
7 -2 6 6 
```

对二维矩阵或阵列的行或者列迭代

对二维表达式的行或列迭代也是可能的。这可以通过`rowwise()`和`colwise()`来完成，下面是一个对没列和行排序的例子：

```c++
ArrayXXi A = ArrayXXi::Random(4,4).abs();
cout << "Here is the initial matrix A:\n" << A << "\n";
for(auto row : A.rowwise())
  std::sort(row.begin(), row.end());
cout << "Here is the sorted matrix A:\n" << A << "\n";
Output:
	
Here is the initial matrix A:
7 9 5 3
2 6 1 0
6 3 0 9
6 6 3 9
Here is the sorted matrix A:
3 5 7 9
0 1 2 6
0 3 6 9
3 6 6 9
```



