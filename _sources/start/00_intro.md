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

# 简介

符号计算
: 符号计算（Symbolic Computation）是以符号的方式处理数学对象的计算。这意味着数学对象被精确的表示，而不是近似的表示，带有未求值的数学表达式保留符号形式。

下面举几个例子来说明符号计算的好处。

## 精确求值

对于 $\sqrt{9}=3$ 这个数学常识，我们一定不会陌生。可以直接使用 Python 内建库求取：

```{code-cell} ipython3
import math
math.sqrt(9)
```

此时，符合数学直观。但是，对于 $\sqrt{8}$ 可能并不一定会那么的准确：

```{code-cell} ipython3
math.sqrt(8)
```

这里得到的是 $\sqrt{8}$ 的近似值（因为 $\sqrt{8}$ 是无理数，不能使用有限小数表示）。如果仅仅是想要获得 $\sqrt{8}$ 的近似值，这样就结束是没有问题的，但是，想要通过下面的近似值立即判断 $\sqrt{8} = 2\sqrt{2}$：

```{code-cell} ipython3
math.sqrt(8), math.sqrt(2)
```

是很难的。对于符号计算，则是很容易的：

```{code-cell} ipython3
import sympy
sympy.sqrt(8)
```

## 数学表达式


上面的例子可以看出 SymPy 可以精确的表达数学概念。符号计算系统（Symbolic computation systems，也称为 CAS（computer algebra systems）） 可以使用变量计算数学表达式：

数学变量是使用 `symbols` 定义的。比如，可以定义数学表达式 $x + 2y$：

```{code-cell} ipython3
from sympy import symbols

x, y = symbols('x y')
expr = x + 2*y
expr
```

可以继续对数学表达式 `expr` 进行计算：

```{code-cell} ipython3
expr + 1
```

```{code-cell} ipython3
expr - x
```

```{code-cell} ipython3
x*expr
```

也可以对数学表达式进行因式分解或者展示：

```{code-cell} ipython3
from sympy import expand, factor

expanded_expr = expand(x*expr)
expanded_expr # 展示表达式
```

```{code-cell} ipython3
factor(expanded_expr) # 转换为因子分解表达式
```

## 求导和求积分

```{code-cell} ipython3
from sympy import *
```

计算：$\sin(x) \exp(x)$ 的导数：

```{code-cell} ipython3
diff(sin(x)*exp(x), x)
```

计算：$\int(e^x\sin{(x)} + e^x\cos{(x)})\mathsf{d}x$

```{code-cell} ipython3
integrate(exp(x)*sin(x) + exp(x)*cos(x), x)
```

计算：$\int_{-\infty}^\infty \sin{(x^2)}\mathsf{d}x$

```{code-cell} ipython3
integrate(sin(x**2), (x, -oo, oo))
```

计算：$\lim_{x\to 0} \frac{\sin{(x)}}{x}$

```{code-cell} ipython3
limit(sin(x)/x, x, 0)
```

## 其他

解方程：$x^2−2=0$

```{code-cell} ipython3
init_printing(use_latex='mathjax')
```

```{code-cell} ipython3
solve(x**2 - 2, x)
```

解微分方程：$y'' - y = e^t$

```{code-cell} ipython3
y = Function('y')
t = symbols('t')
dsolve(Eq(y(t).diff(t, t) - y(t), exp(t)), y(t))
```

计算 $\left[\begin{smallmatrix}1 & 2\\2 &
2\end{smallmatrix}\right]$ 的奇异值：

```{code-cell} ipython3
Matrix([[1, 2], [2, 2]]).eigenvals()
```

使用球面 Bessel 函数 $j_\nu(z)$ 重写 Bessel 函数 $J_{\nu}\left(z\right)$

```{code-cell} ipython3
nu, z = symbols('nu z')
besselj(nu, z).rewrite(jn)
```

```{code-cell} ipython3

```
