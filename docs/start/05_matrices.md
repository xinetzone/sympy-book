---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: ai
  language: python
  name: ai
---

# 矩阵

参考 [Matrices](https://docs.sympy.org/latest/tutorial/matrices.html)


要在 SymPy 中创建矩阵，请使用 `Matrix` 对象。通过提供组成矩阵的行向量列表来构建矩阵。例如，构造矩阵

$$
\begin{split}\left[\begin{array}{cc}1 & -1\\3 & 4\\0 & 2\end{array}\right]\end{split}
$$

```{code-cell} ipython3
import warnings
from sympy import *

warnings.filterwarnings('ignore')
Matrix([[1, -1], [3, 4], [0, 2]])
```

为了便于制作列向量，元素列表被认为是列向量。

```{code-cell} ipython3
Matrix([1, 2, 3])
```

矩阵的操作就像 SymPy 或 Python 中的任何其他对象一样。

```{code-cell} ipython3
M = Matrix([[1, 2, 3], [3, 2, 1]])
N = Matrix([0, 1, 1])
M * N
```

关于 SymPy 矩阵需要注意的一件重要事情是，与 SymPy 中的所有其他对象不同，它们是可变的。这意味着它们可以就地修改，我们将在下面看到。这样做的缺点是 `Matrix` 不能用于需要不变性的地方，例如在其他 SymPy 表达式内部或作为字典的键。如果您需要 `Matrix` 的不可变版本，请使用 `ImmutableMatrix`。

## `Matrix` 的基本运算

### shape

要获得矩阵的形状，请使用 `shape()` 函数。

```{code-cell} ipython3
from sympy import shape
M = Matrix([[1, 2, 3], [-2, 0, 4]])
M
```

```{code-cell} ipython3
shape(M)
```

### 访问行和列

要获取矩阵的单个行或列，请使用 `row` 或 `col`。例如，`M.row(0)` 将获得第一行。`M.col(-1)` 将获得最后一列。

```{code-cell} ipython3
M.row(0), M.col(-1)
```

### 删除和插入行和列

要删除行或列，请使用 `row_del` 或 `col_del`。这些操作将就地修改矩阵。

```{code-cell} ipython3
M.col_del(0)
```

```{code-cell} ipython3
M.row_del(1)
```

要插入行或列，请使用 `row_insert` 或 `col_insert`。这些操作没有就地操作。

```{code-cell} ipython3
M
```

```{code-cell} ipython3
M = M.row_insert(1, Matrix([[0, 4]]))
M
```

```{code-cell} ipython3
M = M.col_insert(0, Matrix([1, -2]))
M
```

除非明确说明，否则下面提到的方法不会就地运行。通常，未就地操作的方法将返回一个新的 `Matrix`，而就地操作的方法将返回 `None`。

## 基础方法

如上所述，加法和乘法等简单运算只需使用 +、* 和 ** 即可完成。要找到矩阵的逆矩阵，只需 -1 次方即可。

```{code-cell} ipython3
M = Matrix([[1, 3], [-2, 3]])
N = Matrix([[0, 3], [0, 7]])
M + N
```

```{code-cell} ipython3
M -N
```

```{code-cell} ipython3
3 * M
```

```{code-cell} ipython3
M * N
```

```{code-cell} ipython3
M ** 2
```

```{code-cell} ipython3
M ** -1
```

要对矩阵进行转置，请使用 `T`。

```{code-cell} ipython3
M = Matrix([[1, 2, 3], [4, 5, 6]])
M
```

```{code-cell} ipython3
M.T
```

## 其他方法

`eye(n)` 生成 $n \times n$ 单位矩阵，`zeros(n, m)`，`ones(n, m)` 分别为全零矩阵，全一矩阵。`diag` 生成对角矩阵：

```{code-cell} ipython3
diag(1, 2, 3)
```

```{code-cell} ipython3
diag(-1, ones(2, 2), Matrix([5, 7, 5]))
```

`det` 计算行列式：

```{code-cell} ipython3
M = Matrix([[1, 0, 1], [2, -1, 3], [4, 3, 2]])
M.det()
```

### `rref`

To put a matrix into reduced row echelon form, use `rref`. `rref` returns a tuple of two elements. The first is the reduced row echelon form, and the second is a tuple of indices of the pivot columns.

```{code-cell} ipython3
M = Matrix([[1, 0, 1, 3], [2, 3, 4, 7], [-1, -3, -3, -4]])
M
```

```{code-cell} ipython3
M.rref()
```

### `nullspace`

To find the nullspace of a matrix, use `nullspace`. `nullspace` returns a `list` of column vectors that span the nullspace of the matrix.

```{code-cell} ipython3
M = Matrix([[1, 2, 3, 0, 0], [4, 10, 0, 0, 1]])
M
```

```{code-cell} ipython3
M.nullspace()
```

### `columnspace`

To find the columnspace of a matrix, use `columnspacee`. `columnspace` returns a `list` of column vectors that span the nullspace of the matrix.

```{code-cell} ipython3
M = Matrix([[1, 1, 2], [2 ,1 , 3], [3 , 1, 4]])
M
```

```{code-cell} ipython3
M.columnspace()
```

### Eigenvalues, Eigenvectors, and Diagonalization

To find the eigenvalues of a matrix, use `eigenvals`. `eigenvals` returns a dictionary of `eigenvalue: algebraic_multiplicity` pairs (similar to the output of [roots](https://docs.sympy.org/latest/tutorial/solvers.html#tutorial-roots)).

```{code-cell} ipython3
M = Matrix([[3, -2,  4, -2], [5,  3, -3, -2], [5, -2,  2, -2], [5, -2, -3,  3]])
M
```

```{code-cell} ipython3
M.eigenvals()
```

This means that `M` has eigenvalues -2, 3, and 5, and that the eigenvalues -2 and 3 have algebraic multiplicity 1 and that the eigenvalue 5 has algebraic multiplicity 2.

To find the eigenvectors of a matrix, use `eigenvects`. `eigenvects` returns a list of tuples of the form `(eigenvalue, algebraic_multiplicity, [eigenvectors])`.

```{code-cell} ipython3
M.eigenvects()
```

This shows us that, for example, the eigenvalue 5 also has geometric multiplicity 2, because it has two eigenvectors. Because the algebraic and geometric multiplicities are the same for all the eigenvalues, `M` is diagonalizable.

To diagonalize a matrix, use diagonalize. diagonalize returns a tuple $(P,D)$, where $D$ is diagonal and $M=PDP^{−1}$.

```{code-cell} ipython3
P, D = M.diagonalize()
P
```

```{code-cell} ipython3
D
```

```{code-cell} ipython3
P*D*P**-1
```

```{code-cell} ipython3
P*D*P**-1 == M
```

Note that since `eigenvects` also includes the eigenvalues, you should use it instead of `eigenvals` if you also want the eigenvectors. However, as computing the eigenvectors may often be costly, `eigenvals` should be preferred if you only wish to find the eigenvalues.

If all you want is the characteristic polynomial, use `charpoly`. This is more efficient than `eigenvals`, because sometimes symbolic roots can be expensive to calculate.

```{code-cell} ipython3
lamda = symbols('lamda')
p = M.charpoly(lamda)
factor(p.as_expr())
```
