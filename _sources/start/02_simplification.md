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

# 化简

参考：[Simplification](https://docs.sympy.org/latest/tutorial/simplification.html)

## `simplify` 是通用的简化方式

```{code-cell} ipython3
from sympy import *

x, y, z = symbols('x:z')
```

`simplify` 可以简化表达式：

```{code-cell} ipython3
simplify(sin(x)**2 + cos(x)**2)
```

```{code-cell} ipython3
simplify((x**3 + x**2 - x - 1)/(x**2 + 2*x + 1))
```

```{code-cell} ipython3
simplify(gamma(x)/gamma(x - 2))
```

这里 `gamma(x)` 指的是：$\Gamma(x)$。

注意：`simplify` 仅仅是简化，不能做到因式分解： 

```{code-cell} ipython3
simplify(x**2 + 2*x + 1)
```

`factor()` 保证将多项式分解为不可约因子。

```{code-cell} ipython3
factor(x**2 + 2*x + 1)
```

## 多项式/有理函数化简

### `expand` 展开多项式

```{code-cell} ipython3
expand((x + 1)**2)
```

```{code-cell} ipython3
expand((x + 2)*(x - 3))
```

给定一个多项式，`expand()` 将把它变成单项式和的规范形式。

`expand()` 听起来可能不像一个简化函数。毕竟，就其名称而言，它使表达式更大，而不是更小。通常是这种情况，但由于 cancellation，表达式在调用 `expand()` 时通常会变小。

```{code-cell} ipython3
expand((x + 1)*(x - 2) - (x - 1)*x)
```

### factor

`factor()` 将多项式分解为有理数上的不可约因子。例如：

```{code-cell} ipython3
factor(x**3 - x**2 + x - 1)
```

```{code-cell} ipython3
factor(x**2*z + 4*x*y*z + 4*y**2*z)
```

对于多项式，`factor()` 与 `expand()` 相反。`factor()` 对有理数使用完整的多元因子分解算法，这意味着 `factor()` 返回的每个因子都保证是不可约的。如果您对因子本身感兴趣，`factor_list` 会返回一个更结构化的输出。

```{code-cell} ipython3
factor_list(x**2*z + 4*x*y*z + 4*y**2*z)
```

请注意，`factor` 和 `expand` 的输入不必是严格意义上的多项式。他们将智能地分解或扩展任何类型的表达式（但请注意，如果输入不再是有理数上的多项式，则这些因子可能不是不可约的）。

```{code-cell} ipython3
expand((cos(x) + sin(x))**2)
```

```{code-cell} ipython3
factor(cos(x)**2 + 2*cos(x)*sin(x) + sin(x)**2)
```

### `collect`

`collect()` 收集表达式中项的公共幂。

```{code-cell} ipython3
expr = x*y + x - 3 + 2*x**2 - z*x**2 + x**3
expr
```

```{code-cell} ipython3
collected_expr = collect(expr, x)
collected_expr
```

`collect()` 与 `.coeff()` 方法结合使用特别有用。`expr.coeff(x, n)` 给出 `expr` 中 $x^n$ 的系数：

```{code-cell} ipython3
collected_expr.coeff(x, 2)
```

### `cancel` 约分

`cancel()` 将采用任何有理函数并将其放入标准规范形式 ${\frac p q}$ 中，其中 $p$ 和 $q$ 是没有公因子的扩展多项式，并且 $p$ 和 $q$ 的前导系数没有分母（即整数）。

```{code-cell} ipython3
cancel((x**2 + 2*x + 1)/(x**2 + x))
```

```{code-cell} ipython3
expr = 1/x + (3*x/2 - 2)/(x - 4)
expr
```

```{code-cell} ipython3
cancel(expr)
```

```{code-cell} ipython3
expr = (x*y**2 - 2*x*y*z + x*z**2 + y**2 - 2*y*z + z**2)/(x**2 - 1)
expr
```

```{code-cell} ipython3
cancel(expr)
```

请注意，由于 `factor()` 将完全分解表达式的分子和分母，因此它也可以用于做同样的事情：

```{code-cell} ipython3
factor(expr)
```

但是，如果您只想确保表达式处于 cancel 形式，`cancel()` 比 `factor()` 更有效。

### apart()
`apart()` 对有理函数执行[部分分式分解](https://en.wikipedia.org/wiki/Partial_fraction_decomposition)。

```{code-cell} ipython3
expr = (4*x**3 + 21*x**2 + 10*x + 12)/(x**4 + 5*x**3 + 5*x**2 + 4*x)
expr
```

```{code-cell} ipython3
apart(expr)
```

## 三角函数简化

### `trigsimp`

要使用三角恒等式简化表达式，请使用 `trigsimp()`。

```{code-cell} ipython3
trigsimp(sin(x)**2 + cos(x)**2)
```

```{code-cell} ipython3
trigsimp(sin(x)**4 - 2*cos(x)**2*sin(x)**2 + cos(x)**4)
```

```{code-cell} ipython3
trigsimp(sin(x)*tan(x)/sec(x))
```

`trigsimp()` 也适用于双曲三角函数。

```{code-cell} ipython3
trigsimp(cosh(x)**2 + sinh(x)**2)
```

```{code-cell} ipython3
trigsimp(sinh(x)/tanh(x))
```

与 `simplify()` 非常相似，`trigsimp()` 将各种三角恒等式应用于输入表达式，然后使用启发式方法返回“最佳”一个。

### `expand_trig`

要扩展三角函数，即应用和或双角恒等式，请使用 `expand_trig()`。

```{code-cell} ipython3
expand_trig(sin(x + y))
```

```{code-cell} ipython3
expand_trig(tan(2*x))
```

由于 `expand_trig()` 倾向于使三角函数表达式更大，而 `trigsimp()` 倾向于使它们更小，因此可以使用 `trigsimp()` 反向应用这些恒等式。

```{code-cell} ipython3
trigsimp(sin(x)*cos(y) + sin(y)*cos(x))
```

## 幂运算

在我们介绍简化幂函数之前，有必要先对幂的恒等式进行数学讨论。指数满足三种恒等式：

1. $x^ax^b = x^{a + b}$
2. $x^ay^a = (xy)^a$，其中 $x, y \geq 0, a \in \mathbb{R}$
3. $(x^a)^b = x^{ab}$ 其中 $b \in \mathbb{Z}$

```{note}
1. 默认情况下，SymPy 符号被假定为复数（\mathbb{C} 的元素）。也就是说，除非它适用于所有复数，否则不会将简化应用于具有给定 Symbol 的表达式。
2. 通过将假设传递给 `symbols()`，可以为符号提供不同的假设。
```

```{code-cell} ipython3
x, y = symbols('x y', positive=True) # 正数
a, b = symbols('a b', real=True) # 实数
z, t, c = symbols('z t c')
```

注意：`sqrt(x)` 是 ` x**Rational(1, 2)` 的简写：

```{code-cell} ipython3
sqrt(x) == x**Rational(1, 2)
```

### `powsimp`

`powsimp()` 从上到下，从左到右应用等式 1 和 2。

```{code-cell} ipython3
powsimp(x**a*x**b)
```

```{code-cell} ipython3
powsimp(x**a*y**a)
```

请注意，如果 `powsimp()` 无效，则拒绝进行简化。

```{code-cell} ipython3
powsimp(t**c*z**c)
```

如果您知道要应用这种简化，但又不想弄乱假设，则可以传递 `force=True` 标志。无论假设如何，这都将强制进行简化。

```{code-cell} ipython3
powsimp(t**c*z**c, force=True)
```

请注意，在某些情况下，特别是当指数是整数或有理数且恒等式 2 成立时，它将自动应用。

```{code-cell} ipython3
(z*t)**2
```

```{code-cell} ipython3
sqrt(x*y)
```

这意味着无法使用 `powsimp()` 撤消此等式，因为即使 `powsimp()` 将基底放在一起，它们也会自动再次分开。

```{code-cell} ipython3
powsimp(z**2*t**2)
```

### `expand_power_exp` / `expand_power_base`

`expand_power_exp()` 和 `expand_power_base()` 分别从右到左应用等式 1 和 2。

```{code-cell} ipython3
expand_power_exp(x**(a + b))
```

```{code-cell} ipython3
expand_power_base((x*y)**a)
```

与 `powsimp()` 一样，如果等式 2 无效，则不会应用它。

```{code-cell} ipython3
expand_power_base((z*t)**c)
```

与 `powsimp()` 一样，您可以使用 `force=True` 强制扩展发生，而无需摆弄假设。

```{code-cell} ipython3
expand_power_base((z*t)**c, force=True)
```

与等式 2 一样，如果幂是数字，则等式 1 会自动应用，因此无法使用 `expand_power_exp()` 撤消。

```{code-cell} ipython3
x**2*x**3
```

```{code-cell} ipython3
expand_power_exp(x**5)
```

### `powdenest`

`powdenest()` 从左到右应用恒等式 3。

```{code-cell} ipython3
powdenest((x**a)**b)
```

和以前一样，如果在给定的假设下不正确，则不应用该等式。

```{code-cell} ipython3
powdenest((z**a)**b)
```

和以前一样，这可以用 `force=True` 手动覆盖。

```{code-cell} ipython3
powdenest((z**a)**b, force=True)
```

## 指数和对数

### `expand_log`

要从左到右应用等式 1 和 2，请使用 `expand_log()`。 与往常一样，除非有效，否则不会应用等式。

```{code-cell} ipython3
x, y = symbols('x y', positive=True)
n = symbols('n', real=True)
```

```{code-cell} ipython3
expand_log(log(x*y))
```

```{code-cell} ipython3
expand_log(log(x/y))
```

```{code-cell} ipython3
expand_log(log(x**2))
```

```{code-cell} ipython3
expand_log(log(x**n))
```

```{code-cell} ipython3
expand_log(log(z*t))
```

与 `powsimp()` 和 `powdenest()` 一样， `expand_log()` 有一个强制选项，可用于忽略假设。

```{code-cell} ipython3
expand_log(log(z**2))
```

```{code-cell} ipython3
expand_log(log(z**2), force=True)
```

### `logcombine`

要从右到左应用等式 1 和 2，请使用 `logcombine()`。

```{code-cell} ipython3
logcombine(log(x) + log(y))
```

```{code-cell} ipython3
logcombine(n*log(x))
```

```{code-cell} ipython3
logcombine(n*log(z))
```

`logcombine()` 也有一个 `force` 选项，可用于忽略假设。

```{code-cell} ipython3
logcombine(n*log(z), force=True)
```

## 特殊函数

SymPy 实现了数十种特殊函数，从组合学中的函数到数学物理。

SymPy 中包含的特殊函数及其文档的详细列表位于[函数模块](https://docs.sympy.org/latest/modules/functions/index.html#functions-contents)页面。

```{code-cell} ipython3
x, y, z = symbols('x y z')
k, m, n = symbols('k m n')
```

$n!= 1\cdot2\cdots(n - 1)\cdot n$

```{code-cell} ipython3
factorial(n)
```

$\binom{n}{k}$

```{code-cell} ipython3
binomial(n, k)
```

$\Gamma(z) = \int_0^\infty t^{z - 1}e^{-t}\,dt$

```{code-cell} ipython3
gamma(z)
```

${}_pF_q\left(\begin{matrix} a_1, \cdots, a_p \\ b_1, \cdots, b_q \end{matrix}
\middle| z \right)$

```{code-cell} ipython3
hyper([1, 2], [3], z)
```

### `rewrite`

处理特殊函数的一种常见方法是将它们相互重写。这适用于 SymPy 中的任何函数，而不仅仅是特殊函数。要根据函数重写表达式，请使用 `expr.rewrite(function)`。例如，

```{code-cell} ipython3
tan(x).rewrite(sin)
```

```{code-cell} ipython3
factorial(x).rewrite(gamma)
```

### `expand_func`

要根据某些等式扩展特殊功能，请使用 `expand_func()`。例如

```{code-cell} ipython3
expand_func(gamma(x + 3))
```

### `hyperexpand`

要根据更标准的函数重写 hyper，请使用 `hyperexpand()`。

```{code-cell} ipython3
hyperexpand(hyper([1, 1], [2], z))
```

`hyperexpand()` 也适用于更通用的 Meijer G 函数

```{code-cell} ipython3
expr = meijerg([[1],[1]], [[1],[]], -z)
expr
```

```{code-cell} ipython3
hyperexpand(expr)
```

### `combsimp`

要简化组合表达式，请使用 `combsimp()`。

```{code-cell} ipython3
n, k = symbols('n k', integer = True)
combsimp(factorial(n)/factorial(n - 3))
```

```{code-cell} ipython3
combsimp(binomial(n+1, k+1)/binomial(n, k))
```

### `gammasimp`

要使用具有非整数参数的 gamma 函数或组合函数来简化表达式，请使用 `gammasimp()`。

```{code-cell} ipython3
gammasimp(gamma(x)*gamma(1 - x))
```

## 示例：连续分数

让我们使用 SymPy 来探索[连分数](https://en.wikipedia.org/wiki/Continued_fraction)。连分数是以下形式的表达式

$$
a_0 + \cfrac{1}{a_1 + \cfrac{1}{a_2 + \cfrac{1}{ \ddots + \cfrac{1}{a_n}
}}}
$$

```{code-cell} ipython3
def list_to_frac(l):
    expr = Integer(0)
    for i in reversed(l[1:]):
        expr += i
        expr = 1/expr
    return l[0] + expr

list_to_frac([x, y, z])
```

```{code-cell} ipython3
list_to_frac([1, 2, 3, 4])
```

```{code-cell} ipython3
syms = symbols('a0:5')
syms
```

```{code-cell} ipython3
frac = list_to_frac(syms)
frac
```

```{code-cell} ipython3
frac = cancel(frac)
frac
```

```{code-cell} ipython3

```
