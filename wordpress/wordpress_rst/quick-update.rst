Quick Update
############
:date: 2010-06-26 03:16
:author: asmeurer
:category: Uncategorized
:slug: quick-update

I've spend most of this week sitting in a car, so while I have been able
to do some work, I haven't had much time to write up a blog post. So, to
comply with `Ondrej's rule`_, here is a quick update.

I have been working my way through Bronstein's book. I finished the
outer algorithmic layer of the implantation. Basically, the algorithm
does polynomials manipulation on the integrand. It first reduces the
integrand into smaller integrals, until it gets to an integral where a
subproblem must be solved to solve it. The subproblem that must be
solved differs depending on the type of the integral. The first one that
comes up in Bronstein's text is the Risch Differential Equation, which
arises from the integration of exponential functions. (I will explain
all of these thing in more detail in a future blog post). At this point,
the algorithms begin to recursively depend on each other, requiring me
to implement more and more algorithms at a time in order for each to
work. To make things worse, a very fundamental set of algorithms are
only described in the text, not given in pseudo-code, so I have had to
figure those things out. These are algorithms to determine if a
differential extension is a derivative or logarithmic derivative of
elements that have already been extended. Again, I will explain better
in a future post, but the idea is that you replace elements in an
integrand with dummy variables, but each element has to be
transcendental over the previous elements. So if you have $latex \\int
(e^x + e^{x^2} + e^{x + x\*^2})dx$, and you set $latex t\_1 = e^x$ and
$latex t\_2 = e^{x^2}$ ($latex Dt\_1 = t\_1$ and $latex Dt\_2 =
2xt\_2$), then you cannot make $latex t\_3 = e^{x + x^2}$ because $latex
e^{x + x^2} = t\_1t\_2$. The ability to determine if an element is a
derivative or a logarithmic derivative of an element of the already
build differential extension is important not only for building up the
extension for the integrand (basically the preparsing), but also for
solving some of the cases of the subproblems such as the Risch
Differential Equation problem.

So I am still figuring out some of the details on that one. The
description in the book is pretty good (this is probably the best
written math textbook I have ever seen), but I still have had to figure
out some of the mathematical details on paper (which is something I
enjoy anyway, but it can be more stressful). Hopefully by the next time
I can have some code that is working enough to actually demonstrate
solving some complex integrals, (with manual preparsing), and even more
excitingly, prove that some non-elementary integrals, such as the
classic $latex \\int e^{-x^2}dx$, are indeed so. And I also hope to have
some more explanations on how the Risch algorithm works in future posts.

.. _Ondrej's rule: http://groups.google.com/group/sympy/browse_thread/thread/7d7dceb34db45302
