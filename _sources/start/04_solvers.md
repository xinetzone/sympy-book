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

# 求解

参考 [Solvers](https://docs.sympy.org/latest/tutorial/solvers.html)

```{code-cell} ipython3
import warnings
from sympy import *

warnings.filterwarnings('ignore')
x, y, z = symbols('x y z')
init_printing(use_latex='mathjax')
```

## 等式

```{code-cell} ipython3
Eq(x, y)
```

解方程：

```{code-cell} ipython3
solveset(Eq(x**2, 1), x)
```

```{code-cell} ipython3
solveset(Eq(x**2 - 1, 0), x)
```

```{code-cell} ipython3
solveset(x**2 - 1, x)
```

如果您希望求解的方程已经等于 0，这将特别有用。您可以使用 `solveset(expr, x)` 而不是键入 `solveset(Eq(expr, 0), x)`。

## 代数求解方程

求解代数方程的主要函数是 `solveset`。`solveset` 的语法是 `solveset(equation, variable=None, domain=S.Complexes)` 其中方程可以是 `Eq` 实例或假定为零的表达式的形式。

请注意，还有另一个称为 `solve` 的函数，它也可用于求解方程。语法是 `solve(equations, variables)` 但是，建议使用 `solveset` 代替。

当求解单个方程时，`solveset` 的输出是一个 `FiniteSet` 或一个 `Interval` 或 `ImageSet`。

```{code-cell} ipython3
solveset(x**2 - x, x)
```

```{code-cell} ipython3
solveset(x - x, x, domain=S.Reals)
```

```{code-cell} ipython3
solveset(sin(x) - 1, x, domain=S.Reals)
```

如果没有解，则返回 `EmptySet`，如果无法找到解，则返回 `ConditionSet`。

```{code-cell} ipython3
solveset(exp(x), x)     # No solution exists
```

```{code-cell} ipython3
solveset(cos(x) - x, x)  # Not able to find solution
```

在 `solveset` 模块中，线性方程组使用 `linsolve` 求解。将来我们将能够直接从 `solveset` 中使用 `linsolve`。以下是 `linsolve` 的语法示例。

- 方程列表形式：

```{code-cell} ipython3
linsolve([x + y + z - 1, x + y + 2*z - 3 ], (x, y, z))
```

- 增广矩阵形式：

```{code-cell} ipython3
linsolve(Matrix(([1, 1, 1, 1], [1, 1, 2, 3])), (x, y, z))
```

- $Ax = b$ 形式：

```{code-cell} ipython3
M = Matrix(((1, 1, 1, 1), (1, 1, 2, 3)))
system = A, b = M[:, :-1], M[:, -1]
linsolve(system, x, y, z)
```

在 `solveset` 模块中，非线性方程组使用 `nonlinsolve` 求解。 以下是 `nonlinsolve` 的示例。

- 当只有实数的解时：

```{code-cell} ipython3
a, b, c, d = symbols('a, b, c, d', real=True)
nonlinsolve([a**2 + a, a - b], [a, b])
```

```{code-cell} ipython3
nonlinsolve([x*y - 1, x - 2], x, y)
```

- 当只有复数的解时：

```{code-cell} ipython3
 nonlinsolve([x**2 + 1, y**2 + 1], [x, y])
```

- 当实解和复解都存在时

```{code-cell} ipython3
system = [x**2 - 2*y**2 -2, x*y - 2]
vars = [x, y]
nonlinsolve(system, vars)
```

```{code-cell} ipython3
system = [exp(x) - sin(y), 1/y - 3]
nonlinsolve(system, vars)
```

- 当系统是正维系统时（有无穷多个解）：

```{code-cell} ipython3
nonlinsolve([x*y, x*y - x], [x, y])
```

```{code-cell} ipython3
system = [a**2 + a*c, a - b]
nonlinsolve(system, [a, b])
```

如果存在以 `LambertW` 形式存在的解，则：

```{code-cell} ipython3
solve([x**2 - y**2/exp(x)], [x, y], dict=True)
```

```{code-cell} ipython3
solve(x*exp(x) - 1, x )
```

三角方程式使用 `solve`：

```{code-cell} ipython3
solve([sin(x + y), cos(x - y)], [x, y])
```

`solveset` 只报告每个解一次。要获得包含多重性的多项式的解，请使用 `roots`。

```{code-cell} ipython3
solveset(x**3 - 6*x**2 + 9*x, x)
```

```{code-cell} ipython3
roots(x**3 - 6*x**2 + 9*x, x)
```

## 求解微分方程

要求解微分方程，请使用 `dsolve`。首先，通过将 `cls=Function` 传递给 `symbols` 函数来创建一个未定义的函数。

```{code-cell} ipython3
f, g = symbols('f g', cls=Function)
```

`f` 和 `g` 现在是未定义的函数。我们可以调用 `f(x)`，它将代表一个未知函数。

```{code-cell} ipython3
f(x)
```

$f(x)$ 的导数也是未计算的。

```{code-cell} ipython3
f(x).diff(x)
```

为了表示微分方程 $f''(x) - 2f'(x) + f(x) = \sin(x)$，我们将使用

```{code-cell} ipython3
diffeq = Eq(f(x).diff(x, x) - 2*f(x).diff(x) + f(x), sin(x))
diffeq
```

要求解 ODE，请将它和要求解的函数传递给 `dsolve`：

```{code-cell} ipython3
dsolve(diffeq, f(x))
```

`dsolve` 返回 `Eq` 的一个实例。这是因为一般来说，微分方程的解不能显式求解该函数。

```{code-cell} ipython3
dsolve(f(x).diff(x)*(1 - sin(f(x))) - 1, f(x))
```

来自 `dsolve` 的解中的任意常数是 `C1`、`C2`、`C3` 等形式的符号。
