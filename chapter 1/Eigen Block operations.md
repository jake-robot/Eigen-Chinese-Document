[Eigen: Block operations](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html)

本文为块操作的概要。块是matrix或array的矩形的一部分，它可以是左值也可以是右值。与Eigen表达式一样，如果让编译器优化，块操作没有运行时间成本。

# 使用块操作

在Eigen中最常见的块操作时`.block()` ，这右两个版本，语法如下：

| **Block** **operation**                    | Version constructing a dynamic-size block expression | Version constructing a fixed-size block expression |
| :----------------------------------------- | :--------------------------------------------------- | :------------------------------------------------- |
| Block of size `(p,q)`, starting at `(i,j)` | matrix.block(i,j,p,q);                               | matrix.block<p,q>(i,j);                            |

Eigen的索引是以0开始的。

两个版本都可以用在固定大小和动态大小的matrices和array上。两种表达式语义上是一致的，只有一点差别即固定的版本会在块比较小的时候快一点，但是要求块大小在编译的时候就知道。

下面的程序使用动态和固定版本去打印matrix中的几个块：

```c++
#include <Eigen/Dense>
#include <iostream>
 
using namespace std;
 
int main()
{
  Eigen::MatrixXf m(4,4);
  m <<  1, 2, 3, 4,
        5, 6, 7, 8,
        9,10,11,12,
       13,14,15,16;
  cout << "Block in the middle" << endl;
  cout << m.block<2,2>(1,1) << endl << endl;
  for (int i = 1; i <= 3; ++i)
  {
    cout << "Block of size " << i << "x" << i << endl;
    cout << m.block(0,0,i,i) << endl << endl;
  }
}
Output:
	
Block in the middle
 6  7
10 11

Block of size 1x1
1

Block of size 2x2
1 2
5 6

Block of size 3x3
 1  2  3
 5  6  7
 9 10 11
```

在上述的例子中`.block()`函数当作一个右值，但是块也可以当作左值。

下面给出在array中使用块：

 ```c++
 #include <Eigen/Dense>
 #include <iostream>
  
 int main()
 {
   Eigen::Array22f m;
   m << 1,2,
        3,4;
   Eigen::Array44f a = Eigen::Array44f::Constant(0.6);
   std::cout << "Here is the array a:\n" << a << "\n\n";
   a.block<2,2>(1,1) = m;
   std::cout << "Here is now a with m copied into its central 2x2 block:\n" << a << "\n\n";
   a.block(0,0,2,3) = a.block(2,1,2,3);
   std::cout << "Here is now a with bottom-right 2x3 block copied into top-left 2x3 block:\n" << a << "\n\n";
 }
 Output:
 	
 Here is the array a:
 0.6 0.6 0.6 0.6
 0.6 0.6 0.6 0.6
 0.6 0.6 0.6 0.6
 0.6 0.6 0.6 0.6
 
 Here is now a with m copied into its central 2x2 block:
 0.6 0.6 0.6 0.6
 0.6   1   2 0.6
 0.6   3   4 0.6
 0.6 0.6 0.6 0.6
 
 Here is now a with bottom-right 2x3 block copied into top-left 2x3 block:
   3   4 0.6 0.6
 0.6 0.6 0.6 0.6
 0.6   3   4 0.6
 0.6 0.6 0.6 0.6
 ```

尽管`.block()`可以用在任何块操作，但仍然右一些API用于特殊情况，可以提供更好的性能。在性能表现上，最重要的是能在编译的前提供给Eigen尽可能多的信息。例如，所使用的块是一列，那么使用`.col()`函数可以让Eigen知道这只是一列，从而给更多的机会去优化。

下文是描述一些特殊情况。

# 列和行

单独的列和行是特殊的块，Eigen提供更容易的方法`.col()`和`.row()`去处理这些块。

| Block operation                                              | Method         |
| :----------------------------------------------------------- | :------------- |
| ith row [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.row(i); |
| jth column [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.col(j); |

`row()`和`col()`的参数是需要访问的行数和列数，在Eigen中是从0开始的。

```c++
#include <Eigen/Dense>
#include <iostream>
 
using namespace std;
 
int main()
{
  Eigen::MatrixXf m(3,3);
  m << 1,2,3,
       4,5,6,
       7,8,9;
  cout << "Here is the matrix m:" << endl << m << endl;
  cout << "2nd Row: " << m.row(1) << endl;
  m.col(2) += 3 * m.col(0);
  cout << "After adding 3 times the first column into the third column, the matrix m is:\n";
  cout << m << endl;
}
Output:
Here is the matrix m:
1 2 3
4 5 6
7 8 9
2nd Row: 4 5 6
After adding 3 times the first column into the third column, the matrix m is:
 1  2  6
 4  5 18
 7  8 30
```

示例还演示了块表达式可以像其它算术表达式一样使用。

# 关于拐角的操作

Eigen还对矩阵或数组的角或边的块提供了特殊的方法，例如：`.topLeftCorner()` 可以用作一个矩阵左上角的块。

下表总结了各种各样的可能：

| Block **operation**                                          | Version constructing a dynamic-size block expression | Version constructing a fixed-size block expression |
| :----------------------------------------------------------- | :--------------------------------------------------- | :------------------------------------------------- |
| Top-left p by q block [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.topLeftCorner(p,q);                           | matrix.topLeftCorner<p,q>();                       |
| Bottom-left p by q block [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.bottomLeftCorner(p,q);                        | matrix.bottomLeftCorner<p,q>();                    |
| Top-right p by q block [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.topRightCorner(p,q);                          | matrix.topRightCorner<p,q>();                      |
| Bottom-right p by q block [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.bottomRightCorner(p,q);                       | matrix.bottomRightCorner<p,q>();                   |
| Block containing the first q rows [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.topRows(q);                                   | matrix.topRows<q>();                               |
| Block containing the last q rows [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.bottomRows(q);                                | matrix.bottomRows<q>();                            |
| Block containing the first p columns [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.leftCols(p);                                  | matrix.leftCols<p>();                              |
| Block containing the last q columns [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.rightCols(q);                                 | matrix.rightCols<q>();                             |
| Block containing the q columns starting from i [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.middleCols(i,q);                              | matrix.middleCols<q>(i);                           |
| Block containing the q rows starting from i [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | matrix.middleRows(i,q);                              | matrix.middleRows<q>(i);                           |

下面是一个简单的例子来解释上述的函数：

```c++
#include <Eigen/Dense>
#include <iostream>
 
using namespace std;
 
int main()
{
  Eigen::Matrix4f m;
  m << 1, 2, 3, 4,
       5, 6, 7, 8,
       9, 10,11,12,
       13,14,15,16;
  cout << "m.leftCols(2) =" << endl << m.leftCols(2) << endl << endl;
  cout << "m.bottomRows<2>() =" << endl << m.bottomRows<2>() << endl << endl;
  m.topLeftCorner(1,3) = m.bottomRightCorner(3,1).transpose();
  cout << "After assignment, m = " << endl << m << endl;
}
Output:
m.leftCols(2) =
 1  2
 5  6
 9 10
13 14

m.bottomRows<2>() =
 9 10 11 12
13 14 15 16

After assignment, m = 
 8 12 16  4
 5  6  7  8
 9 10 11 12
13 14 15 16
```

# 对向量使用块操作

Eigen同样针对一些特殊情况的向量和一维数组提供一些特殊的函数，如下：

| Block operation                                              | Version constructing a dynamic-size block expression | Version constructing a fixed-size block expression |
| :----------------------------------------------------------- | :--------------------------------------------------- | :------------------------------------------------- |
| Block containing the first `n` elements [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | vector.head(n);                                      | vector.head<n>();                                  |
| Block containing the last `n` elements [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | vector.tail(n);                                      | vector.tail<n>();                                  |
| Block containing `n` elements, starting at position `i` [*](http://eigen.tuxfamily.org/dox/group__TutorialBlockOperations.html) | vector.segment(i,n);                                 | vector.segment<n>(i);                              |

示例如下：

```c++
#include <Eigen/Dense>
#include <iostream>
 
using namespace std;
 
int main()
{
  Eigen::ArrayXf v(6);
  v << 1, 2, 3, 4, 5, 6;
  cout << "v.head(3) =" << endl << v.head(3) << endl << endl;
  cout << "v.tail<3>() = " << endl << v.tail<3>() << endl << endl;
  v.segment(1,4) *= 2;
  cout << "after 'v.segment(1,4) *= 2', v =" << endl << v << endl;
}
Output:
v.head(3) =
1
2
3

v.tail<3>() = 
4
5
6

after 'v.segment(1,4) *= 2', v =
 1
 4
 6
 8
10
 6
```

