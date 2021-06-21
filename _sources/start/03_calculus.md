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

# 计算

参考 [Calculus](https://docs.sympy.org/latest/tutorial/calculus.html)

本节介绍如何在 SymPy 中执行基本的微积分任务，例如导数、积分、极限和级数展开。

```{code-cell} ipython3
import warnings
from sympy import *

warnings.filterwarnings('ignore')
x, y, z = symbols('x y z')
```

## 微分

微分（derivatives）使用 `diff` 函数：

```{code-cell} ipython3
diff(cos(x), x)
```

```{code-cell} ipython3
diff(exp(x**2), x)
```

`diff` 可以一次取多个导数。要获取多个导数，请根据您希望微分的次数传递变量，或者在变量后传递一个数字。例如，下面两个都找到了 $x^4$ 的三阶导数。

```{code-cell} ipython3
diff(x**4, x, x, x)
```

```{code-cell} ipython3
diff(x**4, x, 3)
```

您还可以一次对许多变量进行导数。只需按顺序传递每个导数，使用与单变量导数相同的语法。

例如：计算 $\frac{\partial^7}{\partial x\partial y^2\partial z^4} e^{x y z}$

```{code-cell} ipython3
expr = exp(x*y*z)
diff(expr, x, y, y, z, z, z, z)
```

```{code-cell} ipython3
diff(expr, x, y, 2, z, 4)
```

```{code-cell} ipython3
diff(expr, x, y, y, z, 4)
```

`diff` 也可以称为方法。这两种调用 `diff` 的方式完全一样，提供只是为了方便。

```{code-cell} ipython3
expr.diff(x, y, y, z, 4)
```

要创建未计算的导数，请使用 `Derivative` 类。它的语法与 `diff` 相同。

```{code-cell} ipython3
deriv = Derivative(expr, x, y, y, z, 4)
deriv
```

要计算未评估的导数，请使用 `doit` 方法。

```{code-cell} ipython3
deriv.doit()
```

这些未求值的对象对于延迟导数的求值或用于打印目的很有用。当 SymPy 不知道如何计算表达式的导数时（例如，如果它包含未定义的函数，这在求解微分方程部分中进行了描述），它们也会被使用。

可以使用元组 `(x, n)` 创建未指定阶的导数，其中 `n` 是导数相对于 `x` 的阶数。

```{code-cell} ipython3
m, n, a, b = symbols('m n a b')
expr = (a*x + b)**m
expr.diff((x, n))
```

## 积分

要计算积分，请使用 `integrate` 函数。积分有定积分和不定积分两种。要计算不定积分，即反导数（antiderivative）或原语（primitive），只需在表达式后传递变量即可。

```{code-cell} ipython3
integrate(cos(x), x)
```

要计算定积分，请传递参数 `(integration_variable, lower_limit, upper_limit)`。

例如，计算：$\int_0^\infty e^{-x}\,dx,$

```{code-cell} ipython3
integrate(exp(-x), (x, 0, oo))
```

与不定积分一样，您可以传递多个极限元组来执行多重积分。例如，要计算

$$
\int_{-\infty}^{\infty}\int_{-\infty}^{\infty} e^{- x^{2} - y^{2}}\, dx\, dy,
$$

```{code-cell} ipython3
integrate(exp(-x**2 - y**2), (x, -oo, oo), (y, -oo, oo))
```

如果 `integrate` 无法计算积分，则返回未计算的 `Integral` 对象。

```{code-cell} ipython3
expr = integrate(x**x, x)
expr
```

```{code-cell} ipython3
print(expr)
```

与 `Derivative` 一样，您可以使用 `Integral` 创建未计算的积分。稍后要评估这个积分，请调用 `doit`。

```{code-cell} ipython3
expr = Integral(log(x)**2, x)
expr
```

```{code-cell} ipython3
expr.doit()
```

集成使用强大的算法来计算定积分和不定积分，包括启发式模式匹配类型算法、[Risch 算法](https://en.wikipedia.org/wiki/Risch_algorithm)的部分实现以及使用 [Meijer G 函数](https://en.wikipedia.org/wiki/Meijer_g-function)的算法，该算法对于计算特殊函数的积分很有用 ，尤其是定积分。以下是一些集成函数的示例。

```{code-cell} ipython3
integ = Integral((x**4 + x**2*exp(x) - x**2 - 2*x*exp(x) - 2*x - \
                  exp(x))*exp(x)/((x - 1)**2*(x + 1)**2*(exp(x) + 1)), x)
integ
```

```{code-cell} ipython3
integ.doit()
```

```{code-cell} ipython3
integ = Integral(sin(x**2), x)
integ
```

```{code-cell} ipython3
integ.doit()
```

```{code-cell} ipython3
integ = Integral(x**y*exp(-x), (x, 0, oo))
integ
```

```{code-cell} ipython3
integ.doit()
```

## 极限

计算：

$$
\lim_{x\to x_0} f(x)
$$

使用 `limit(f(x), x, x0)`。

```{code-cell} ipython3
limit(sin(x)/x, x, 0)
```

当评估点是奇点（singularity）时，应使用 `limit` 而不是 `subs`。尽管 SymPy 有表示 $\infty$ 的对象，但使用它们进行评估并不可靠，因为它们不会跟踪诸如增长率之类的事情。此外，诸如 $\infty - \infty$ 和 $\frac{\infty}{\infty}$ 之类的东西返回 nan （非数字）。

```{code-cell} ipython3
expr = x**2/exp(x)
expr.subs(x, oo)
```

```{code-cell} ipython3
limit(expr, x, oo)
```

与 `Derivative` 和 `Integral` 一样，`limit` 也有一个未计算的对应物 `Limit`。要评估它，请使用 `doit`。

```{code-cell} ipython3
expr = Limit((cos(x) - 1)/x, x, 0)
expr
```

```{code-cell} ipython3
expr.doit()
```

要仅评估单侧极限，请将“`+`”或“`-`”作为 `limit` 的第四个参数传递。例如，要计算

$$
\lim_{x\to 0^+}\frac{1}{x},
$$

```{code-cell} ipython3
limit(1/x, x, 0, '+')
```

```{code-cell} ipython3
limit(1/x, x, 0, '-')
```

## 级数展开

SymPy 可以计算函数围绕一个点的渐近级数展开。要计算 $f(x)$ 围绕 $x^n$ 阶项 $x=x_0$ 项的展开，请使用 `f(x).series(x, x0, n). x0` 和 `n` 可以省略，在这种情况下，将使用默认值 `x0=0` 和 `n=6`。

```{code-cell} ipython3
expr = exp(sin(x))
expr.series(x, 0, 4)
```

最后的 $O\left(x^4\right)$ 项表示 $x=0$ 处的 Landau 阶项。

```{code-cell} ipython3
x + x**3 + x**6 + O(x**4)
```

```{code-cell} ipython3
x*O(1)
```

如果您不想要 $O$ 项，请使用 `removeO` 方法。

```{code-cell} ipython3
expr.series(x, 0, 4).removeO()
```

$O$ 表示法支持任意限制点（0 除外）：

```{code-cell} ipython3
exp(x - 6).series(x, x0=6)
```

## 有限差分

到目前为止，我们已经分别研究了具有解析导数和原始函数的表达式。但是，如果我们想要一个表达式来估计一条曲线的导数，而该曲线缺少封闭形式表示，或者我们还不知道其函数值，该怎么办？一种方法是使用有限差分（Finite differences）方法。

使用有限差分进行微分的最简单方法是使用 `distinct_finite` 函数：

```{code-cell} ipython3
f, g = symbols('f g', cls=Function)

differentiate_finite(f(x)*g(x))
```

如果您已经有一个 `Derivative` 实例，您可以使用 `as_finite_difference` 方法生成任意阶导数的近似值：

```{code-cell} ipython3
f = Function('f')

dfdx = f(x).diff(x)
dfdx
```

```{code-cell} ipython3
dfdx.as_finite_difference()
```

在这里，使用最小数量的点（一阶导数为 2）使用步长为 1 等距计算一阶导数围绕 x 近似。我们可以使用任意步长（可能包含符号表达式）：

```{code-cell} ipython3
f = Function('f')
d2fdx2 = f(x).diff(x, 2)
h = Symbol('h')
d2fdx2.as_finite_difference([-3*h,-h,2*h])
```

如果您只是对评估权重感兴趣，您可以手动进行：

```{code-cell} ipython3
finite_diff_weights(2, [-3, -1, 2], 0)[-1][-1]
```

请注意，我们只需要从 `finite_diff_weights` 返回的最后一个子列表中的最后一个元素。这样做的原因是该函数还为较低的导数生成权重并使用较少的点（有关更多详细信息，请参阅 `finite_diff_weights` 的文档）。

如果直接使用 `finite_diff_weights` 看起来复杂，并且 `Derivative` 实例的 `as_finite_difference` 方法不够灵活，可以使用以 `order`、`x_list`、`y_list` 和 `x0` 为参数的 `apply_finite_diff`：

```{code-cell} ipython3
x_list = [-3, 1, 2]
y_list = symbols('a b c')
apply_finite_diff(1, x_list, y_list, 0)
```

```{code-cell} ipython3

```
