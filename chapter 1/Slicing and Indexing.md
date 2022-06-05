[Eigen: Slicing and Indexing](http://eigen.tuxfamily.org/dox/group__TutorialSlicingIndexing.html)



本文展示如果使用操作运算符`operator()` 来索引行和列的子集。这个API是在Eigen 3.4中完成的。它拥有 [block API](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html)提供的所有功能，甚至更多。特别的，它支持包含提取一行、一列、单个元素的操作以及等间隔的从矩阵或者数组中提取元素。

# 概述

上述所以提到的操作都是用`DenseBase::operator()(const RowIndices&, const ColIndices&)`来完成的，每一个参数可以是：

- 索引单行或列的整数，包括符号索引
- 符号Eigen::all表示按递增顺序排列的所有行或列
- 由 `Eigen::seq`,` Eigen::seqN`或者` Eigen::placeholders::lastN` 函数构造的[ArithmeticSequence](http://eigen.tuxfamily.org/dox/classEigen_1_1ArithmeticSequence.html)
- 任意一维整数向量、阵列，形式如Eigen数组、阵列、表达式，`std::vector` , C的数组`int[N]`

更一般的，该函数可以接受任何有下列两个成员函数接口的对象

```c++
<integral type> operator[](<integral type>) const;

<integral type> size() const;
```

其中`<integral type>` 代表任何可以与`Eigen::index`兼容的整数，如`std::ptrdiff_t`

# 基本的切片

通过`Eigen::seq`或`Eigen::seqN`函数，取矩阵或向量中均匀间隔的一组行、列或元素，其中seq代表等差数列。他们的用法如下:

| function                                                     | description                                   | example                                                      |
| :----------------------------------------------------------- | :-------------------------------------------- | :----------------------------------------------------------- |
| [seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(firstIdx,lastIdx) | 返回从`firstIdx` 到 `lastIdx`的整数           | [seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(2,5) <=> {2,3,4,5} |
| [seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(firstIdx,lastIdx,incr) | 与上面相同，但是索引每次增加incr              | [seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(2,8,2) <=> {2,4,6,8} |
| [seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(firstIdx,size) | 从`firstIdx`开始，索引每次加1，总的个数为size | [seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(2,5) <=> {2,3,4,5,6} |
| [seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(firstIdx,size,incr) | 与上述相同，索引每次增加incr                  | [seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(2,3,3) <=> {2,5,8} |

一旦等差序列通过operator()传递给它，`firststidx`和`lasttidx`参数也可以用`Eigen::last`符号来定义，该符号表示矩阵/向量的最后一行、最后一列或元素的索引，使用如下：

| Intent                                                   | Code                                                         | Block-API equivalence            |
| :------------------------------------------------------- | :----------------------------------------------------------- | :------------------------------- |
| Bottom-left corner starting at row `i` with `n` columns  | A([seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(i,[last](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga66661a473fe06e47e3fd5c591b6ffe8d)), [seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(0,n)) | A.bottomLeftCorner(A.rows()-i,n) |
| Block starting at `i`,j having `m` rows, and `n` columns | A([seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(i,m), [seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(i,n)) | A.block(i,j,m,n)                 |
| Block starting at `i0`,j0 and ending at `i1`,j1          | A([seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(i0,i1), [seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(j0,j1) | A.block(i0,j0,i1-i0+1,j1-j0+1)   |
| Even columns of A                                        | A([all](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga4abe6022fbef6cda264ef2947a2be1a9), [seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(0,[last](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga66661a473fe06e47e3fd5c591b6ffe8d),2)) |                                  |
| First `n` odd rows A                                     | A([seqN](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a3a3c346d2a61d1e8e86e6fb4cf57fbda)(1,n,2), [all](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga4abe6022fbef6cda264ef2947a2be1a9)) |                                  |
| The last past one column                                 | A([all](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga4abe6022fbef6cda264ef2947a2be1a9), [last](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga66661a473fe06e47e3fd5c591b6ffe8d)-1) | A.col(A.cols()-2)                |
| The middle row                                           | A([last](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga66661a473fe06e47e3fd5c591b6ffe8d)/2,[all](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga4abe6022fbef6cda264ef2947a2be1a9)) | A.row((A.rows()-1)/2)            |
| Last elements of v starting at i                         | v([seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)(i,[last](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga66661a473fe06e47e3fd5c591b6ffe8d))) | v.tail(v.size()-i)               |
| Last `n` elements of v                                   | v([seq](http://eigen.tuxfamily.org/dox/namespaceEigen.html#a0c04400203ca9b414e13c9c721399969)([last](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga66661a473fe06e47e3fd5c591b6ffe8d)+1-n,[last](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga66661a473fe06e47e3fd5c591b6ffe8d))) | v.tail(n)                        |

正如在上一个示例中看到的，引用最后n个元素(或行/列)编写起来有点麻烦。使用非默认增量时，这将变得更加棘手和容易出错。因此，Eigen提供了[Eigen::placeholders::lastN(size)](http://eigen.tuxfamily.org/dox/group__TutorialSlicingIndexing.html)和[Eigen::placeholders::lastN(size,incr) ](http://eigen.tuxfamily.org/dox/group__TutorialSlicingIndexing.html)函数来完成最后几个元素的提取，用法如下：

|                                                |                                                              |                          |
| :--------------------------------------------- | :----------------------------------------------------------- | :----------------------- |
| Intent                                         | Code                                                         | Block-API equivalence    |
| Last `n` elements of v                         | v(lastN(n))                                                  | v.tail(n)                |
| Bottom-right corner of A of size `m` times `n` | v(lastN(m), lastN(n))                                        | A.bottomRightCorner(m,n) |
| Bottom-right corner of A of size `m` times `n` | v(lastN(m), lastN(n))                                        | A.bottomRightCorner(m,n) |
| Last `n` columns taking 1 column over 3        | A([all](http://eigen.tuxfamily.org/dox/group__Core__Module.html#ga4abe6022fbef6cda264ef2947a2be1a9), lastN(n,3)) |                          |

# 编译时的大小和增量

在性能方面，Eigen和编译器可以利用编译时的大小和增量。为了这个目的，你可以使用`Eigen::fix<val>`在编译时刻强制指定大小。而且，它可以和`Eigen::last`符号一起使用：

```c++
v(seq(last-fix<7>, last-fix<2>))
```

在这个例子中，Eigen在编译时就知道返回的表达式有6个元素。它等价于:

```c++
v(seqN(last-7, fix<6>))
```

我们可以查看A的偶数列，如：

```c++
A(all, seq(0,last,fix<2>))
```

# 倒序

我们可以把增量设置为负数，如该例子实现A的20到10列中取一半：

```c++
A(all, seq(20, 10, fix<-2>))
```

从最后一个开始，取n个数：

```c++
A(seqN(last, n, fix<-1>), all)
```

也可以使用函数`ArithmeticSequence::reverse() `方法来反转序列，之前的例子也可以写作：

```c++
A(lastN(n).reverse(), all)
```

# 数组的索引

`operator()`输入的也可以是`ArrayXi`, `std::vector<int>`, `std::array<int,N>`，如：

```c++
Example:	
std::vector<int> ind{4,2,5,5,3};
MatrixXi A = MatrixXi::Random(4,6);
cout << "Initial matrix A:\n" << A << "\n\n";
cout << "A(all,ind):\n" << A(Eigen::placeholders::all,ind) << "\n\n";
Output:
Initial matrix A:
  7   9  -5  -3   3 -10
 -2  -6   1   0   5  -5
  6  -3   0   9  -8  -8
  6   6   3   9   2   6

A(all,ind):
  3  -5 -10 -10  -3
  5   1  -5  -5   0
 -8   0  -8  -8   9
  2   3   6   6   9


```

```c++
MatrixXi A = MatrixXi::Random(4,6);
cout << "Initial matrix A:\n" << A << "\n\n";
cout << "A(all,{4,2,5,5,3}):\n" << A(Eigen::placeholders::all,{4,2,5,5,3}) << "\n\n";
Output:
Initial matrix A:
  7   9  -5  -3   3 -10
 -2  -6   1   0   5  -5
  6  -3   0   9  -8  -8
  6   6   3   9   2   6

A(all,{4,2,5,5,3}):
  3  -5 -10 -10  -3
  5   1  -5  -5   0
 -8   0  -8  -8   9
  2   3   6   6   9
```

```c++
ArrayXi ind(5); ind<<4,2,5,5,3;
MatrixXi A = MatrixXi::Random(4,6);
cout << "Initial matrix A:\n" << A << "\n\n";
cout << "A(all,ind-1):\n" << A(Eigen::placeholders::all,ind-1) << "\n\n";
Output:
Initial matrix A:
  7   9  -5  -3   3 -10
 -2  -6   1   0   5  -5
  6  -3   0   9  -8  -8
  6   6   3   9   2   6

A(all,ind-1):
-3  9  3  3 -5
 0 -6  5  5  1
 9 -3 -8 -8  0
 9  6  2  2  3
```

当传递一个具有编译时大小的对象(如`Array4i`、`std::array<int, N>`或静态数组)时，返回的表达式也会显示编译时维度。

# 自定义索引列表

更一般的，`operator()`可以接受任何与下列兼容的T类型的对象ind：

```c++
Index s = ind.size(); or Index s = size(ind);
Index i;
i = ind[i];
```

这意味着您可以轻松地构建自己的序列生成器并将其传递给operator()。下面是一个例子，扩大一个给定的矩阵，同时通过重复填充额外的第一行和列:

```c++
struct pad {
  Index size() const { return out_size; }
  Index operator[] (Index i) const { return std::max<Index>(0,i-(out_size-in_size)); }
  Index in_size, out_size;
};
 
Matrix3i A;
A.reshaped() = VectorXi::LinSpaced(9,1,9);
cout << "Initial matrix A:\n" << A << "\n\n";
MatrixXi B(5,5);
B = A(pad{3,5}, pad{3,5});
cout << "A(pad{3,N}, pad{3,N}):\n" << B << "\n\n";

Output:
Initial matrix A:
1 4 7
2 5 8
3 6 9

A(pad{3,N}, pad{3,N}):
1 1 1 4 7
1 1 1 4 7
1 1 1 4 7
2 2 2 5 8
3 3 3 6 9
```

