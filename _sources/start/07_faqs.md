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

# 常见问题

## 等式使用：`Eq`

可以借助 [`expand()`](https://docs.sympy.org/latest/modules/core.html#sympy.core.function.expand "sympy.core.function.expand"), [`simplify()`](https://docs.sympy.org/latest/modules/simplify/simplify.html#sympy.simplify.simplify.simplify "sympy.simplify.simplify.simplify") 和 [`trigsimp()`](https://docs.sympy.org/latest/modules/simplify/simplify.html#sympy.simplify.trigsimp.trigsimp "sympy.simplify.trigsimp.trigsimp") 进行真值判断：

```{code-cell} ipython3
import warnings
from sympy import simplify, cos, sin, expand, init_printing
from sympy import symbols
warnings.filterwarnings('ignore')
init_printing()

x = symbols('x')
simplify((x + 1)**2 - (x**2 + 2*x + 1))
```

```{code-cell} ipython3
eq = sin(2*x) - 2*sin(x)*cos(x)
simplify(eq)
```

```{code-cell} ipython3
expand(eq, trig=True)
```

## 变量

```{code-cell} ipython3
from sympy import Symbol
```

符号 $a$ 被存储为变量： "`a`"

```{code-cell} ipython3
a = Symbol('a')
a
```

将包含符号 $a$ 的表达式存储为变量 "`b`"

```{code-cell} ipython3
b = a + 1
b
```

```{code-cell} ipython3
a = 4
a
```

```{code-cell} ipython3
b
```

```{code-cell} ipython3
from sympy import var

r, t, d = var('rate time short_life')
r, t, d
```

```{code-cell} ipython3
c, d = var('c d')
c*d
```

```{code-cell} ipython3
s, t, w = symbols('s t w')
s, t, w
```

```{code-cell} ipython3
var('ww er, ty')
```

```{code-cell} ipython3
ww
```

```{code-cell} ipython3
symbols('rrt')
# rrt 会报错
```

推荐使用 `symbols` 创建变量。

```{code-cell} ipython3
x, y, z = symbols('x:z')
```

```{code-cell} ipython3
symbols('x:4')
```

## 函数

```{code-cell} ipython3
from sympy import Function
```

```{code-cell} ipython3
f = Function('f')
x = symbols('x')

f(x)
```

```{code-cell} ipython3
f1 = Function('f1')
f2 = Function('f2')(x)
```

$f_1$ 是可调用的，但 $f2$ 不行：

```{code-cell} ipython3
f1(1), f1(x)
```

```{code-cell} ipython3
# f2(1) 会报错
```

