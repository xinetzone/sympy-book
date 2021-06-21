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

# 基本运算

定义一些基本数学变量：

```{code-cell} ipython3
import warnings
from sympy import *

warnings.filterwarnings('ignore')
# init_printing()

x, y, z = symbols('x y z')
```

## 置换

置换（substitution）即把某个变量替换为另一个变量。使用 `subs` 方法实现：

```{code-cell} ipython3
expr = cos(x) + 1
expr.subs(x, y) # x 置换为 y
```

需要**置换**的场景：

1. 求解具体的表达式代表的值，比如：

```{code-cell} ipython3
expr.subs(x, 0)
```

2. 变量替换：

```{code-cell} ipython3
expr = x**y
expr = expr.subs(y, x**y)
expr
```

```{code-cell} ipython3
expr = expr.subs(y, x**x)
expr
```

```{code-cell} ipython3
expr = sin(2*x) + cos(2*x)
expand_trig(expr) # 三角展开
```

```{code-cell} ipython3
expr.subs(sin(2*x), 2*sin(x)*cos(x))
```

```{tip}
SymPy 表达式是不可变的对象。
```

替换多个变量可以这样：

```{code-cell} ipython3
expr = x**3 + 4*x*y - z
expr.subs([(x, 2), (y, 4), (z, 0)])
```

甚至，可以制定替换的规则：

```{code-cell} ipython3
expr = x**4 - 4*x**3 + 4*x**2 - 2*x + 3
replacements = [(x**i, y**i) for i in range(5) if i % 2 == 0]
expr.subs(replacements)
```

## 转换字符串为 SymPy 表达式

`sympify` 可以将字符串转换为 SymPy 表达式：

```{code-cell} ipython3
str_expr = "x**2 + 3*x - 1/2"
expr = sympify(str_expr)
expr
```

```{code-cell} ipython3
expr.subs(x, 2)
```

### `evalf`

`evalf` 将数值表达式转换为浮点数：

```{code-cell} ipython3
expr = sqrt(8)
expr.evalf()
```

可以浮点数设置精确的位数：

```{code-cell} ipython3
pi.evalf(100)
```

为了精确的计算数值表达式，可以这样：

```{code-cell} ipython3
expr = cos(2*x)
expr.evalf(subs={x: 2.4})
```

有时舍入误差小于期望精度，可能会这样：

```{code-cell} ipython3
one = cos(1)**2 + sin(1)**2
(one - 1).evalf()
```

此时，可以：

```{code-cell} ipython3
(one - 1).evalf(chop=True)
```

### `lambdify`

如果数据量很大，需要借助 [NumPy](http://www.numpy.org/) 和 [SciPy](http://www.scipy.org/)：

```{code-cell} ipython3
import numpy as np
a = np.arange(10)
expr = sin(x)
f = lambdify(x, expr, "numpy")
f(a)
```

也可以自定义数值计算函数：

```{code-cell} ipython3
def mysin(x):
    """
    My sine. Note that this is only accurate for small x.
    """
    return np.sin(x)

f = lambdify(x, expr, {"sin":mysin})

f(0.1)
```

```{code-cell} ipython3

```
