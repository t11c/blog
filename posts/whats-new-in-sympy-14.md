*This post has been cross-posted on the [Quansight Blog](...).*

SymPy 1.4 was released on April 9, 2019. In this post, I'd like to go over
some of the highlights for this release. The full release notes for the
release can be found on the [SymPy
wiki](https://github.com/sympy/sympy/wiki/Release-Notes-for-1.4).

To update to SymPy 1.4, use

```bash
conda install sympy
```

or if you prefer to use pip

```bash
pip install -U sympy
```

The SymPy 1.4 release contains over 500 changes from 38 different submodules,
so I will not be going over every change, but only a few of the main
highlights. A [total of 104
people](https://github.com/sympy/sympy/wiki/Release-Notes-for-1.4#authors)
contributed to this release, including 66 people contributed for the first
time for this release.

# Automatic LaTeX rendering in the Jupyter notebook

Prior to SymPy 1.4, SymPy expressions in the notebook rendered with their
default string representation, unless `init_printing()` was called, after
which they rendered with LaTeX:

<img src="../../sympy-1.3-notebook.png" alt="SymPy 1.3 rendering in the Jupyter lab notebook">

In SymPy 1.4, SymPy expressions now automatically render as LaTeX in the notebook:

<img src="../../sympy-1.4-notebook.png" alt="SymPy 1.4 rendering in the Jupyter lab notebook">

However, this only applies automatically if the type of an object is a SymPy
expression. For built-in types such as lists or ints, `init_printing()` is
still required to get LaTeX printing. For example, `solve()` returns a list,
so does not render as LaTeX unless `init_printing()` is called:

<img src="../../sympy-1.4-notebook-2.png" alt="SymPy 1.4 rendering in the Jupyter lab notebook with init_printing()">


# Improved simplification of relational expressions

simplification of relational and piecewise expressions has been improved:

```py
>>> x, y, z, w = symbols('x y z w')
>>> init_printing()
>>> And(Eq(x,y), x >= y, w < y, y >= z, z < y)
x = y ∧ x ≥ y ∧ y ≥ z ∧ w < y ∧ z < y
>>> simplify(And(Eq(x,y), x >= y, w < y, y >= z, z < y))
x = y ∧ y > Max(w, z)
```

```py
>>> Piecewise((x*y, And(x >= y, Eq(y, 0))), (x - 1, Eq(x, 1)), (0, True))
⎧ x⋅y   for y = 0 ∧ x ≥ y
⎪
⎨x - 1      for x = 1
⎪
⎩  0        otherwise
>>> simplify(Piecewise((x*y, And(x >= y, Eq(y, 0))), (x - 1, Eq(x, 1)), (0, True)))
0
```

# Improved MathML printing

The MathML presentation printer has been greatly improved, putting it on par
with the existing Unicode and LaTeX pretty printers.

```py
>>> mathml(Integral(exp(-x**2), (x, -oo, oo)), 'presentation')
<mrow><msubsup><mo>&#x222B;</mo><mrow><mo>-</mo><mi>&#x221E;</mi></mrow><mi>&#x221E;</mi></msubsup><msup><mi>&ExponentialE;</mi><mrow><mo>-</mo><msup><mi>x</mi><mn>2</mn></msup></mrow></msup><mo>&dd;</mo><mi>x</mi></mrow>
```

If your [browser supports MathML](https://caniuse.com/#feat=mathml) (at the
time of writing, only Firefox and Safari), you should see the above
presentation form for `Integral(exp(-x**2), (x, -oo, oo))` below:

<math style="display: block;"><mrow><msubsup><mo>&#x222B;</mo><mrow><mo>-</mo><mi>&#x221E;</mi></mrow><mi>&#x221E;</mi></msubsup><msup><mi>&ExponentialE;</mi><mrow><mo>-</mo><msup><mi>x</mi><mn>2</mn></msup></mrow></msup><mo>&dd;</mo><mi>x</mi></mrow></math>

# Dropping Python 3.4 support

This is the last release of SymPy to support Python 3.4. SymPy 1.4 supports
Python 2.7, 3.4, 3.5, 3.6, 3.7, and PyPy. What's perhaps more exciting is that
the next release of SymPy, 1.5, which will be released later this year, will
be the last version to support Python 2.7.

Our
[policy](https://github.com/sympy/sympy/wiki/Python-version-support-policy) is
to drop support for major Python versions when they reach their [End of
Life](https://devguide.python.org/#status-of-python-branches). In other words,
they receive no further support from the CPython team. Python 3.4 reached its
end of life on May 19 of this year, and Python 2.7 will reach its end of life
on January 1, 2020.

I have [blogged in the
past](https://www.asmeurer.com/blog/posts/moving-away-from-python-2/) on why I
believe it is important for library authors to be proactive in dropping Python
2 support, and since then [a large number of Python
libraries](https://python3statement.org) have either dropped support or
announced their plans to by 2020.

Having Python 2 support removed will not only allow us to remove a [large
amount of compatibility
cruft](https://github.com/sympy/sympy/blob/sympy-1.4/sympy/core/compatibility.py)
from our codebase, it will also allow us to use some Python 3-only features
that will allow us to clean up our API, such as [keyword-only
arguments](https://python-3-for-scientists.readthedocs.io/en/latest/python3_advanced.html#keyword-only-arguments),
[type
hints](https://python-3-for-scientists.readthedocs.io/en/latest/python3_features.html#function-annotations),
and [Unicode variable
names](https://python-3-for-scientists.readthedocs.io/en/latest/python3_features.html#unicode-variable-names).
It will also enable [several internal
changes](https://github.com/sympy/sympy/issues?q=is%3Aissue+is%3Aopen+label%3A"Dropping+Python+2")
that will not be visible to end-users, but which will result in a much cleaner
codebase.

If you are still using Python 2, I strongly recommend switching to Python 3,
as otherwise the entire ecosystem of Python libraries is soon going to stop
improving for you. Python 3 is already highly recommended for SymPy usage due
to several key improvements. In particular, in Python 3 division of two
integers like `1/2` produces the float `0.5` instead of doing integer division
(which produces `0`). The Python 2 integer division behavior can lead to very
surprising results when using SymPy (imagine writing `x**2 + 1/2*x + 2` and
having the `x` term "disappear"). When using SymPy, we recommend [avoiding
writing
`int/int`](https://docs.sympy.org/latest/tutorial/gotchas.html#two-final-notes-and)
and instead using rational numbers (like `Rational(1, 2)`), but the Python 3
behavior will at least maintain a mathematically correct result if you do not
do this. SymPy is also already faster in Python 3 due to things like
`math.gcd` and `functools.lru_cache` being written in C, and general
performance improvements in the interpreter itself.