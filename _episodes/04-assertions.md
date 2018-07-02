---
title: Assertions
teaching: 10
exercises: 0
questions:
- "How can we compare observed and expected values?"
objectives:
- "Assertions are one line tests embedded in code."
- "Assertions can halt execution if something unexpected happens."
- "Assertions are the building blocks of tests."
keypoints:
- "Assertions are one line tests embedded in code."
- "The `assert` keyword is used to set an assertion."
- "Assertions halt execution if the argument is false."
- "Assertions do nothing if the argument is true."
- "The `numpy.testing` module provides tools numeric testing."
- "Assertions are the building blocks of tests."
---

Assertions are the simplest type of test. They are used as a tool for bounding
acceptable behavior during runtime. The assert keyword in python has the
following behavior:

~~~
>>> assert True == False
~~~
{: .python}
~~~
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  AssertionError
~~~
{: .error}
~~~
>>> assert True == True
~~~
{: .python}
~~~
~~~
{: .output}

That is, assertions raise an `AssertionError` if the comparison is false.
It does nothing at all if the comparison is true. Assertions are therefore a
simple way of writing tests.
~~~
>>> assert mean([1,2,3]) == 2
~~~
{: .python}
~~~
~~~
{: .output}


> ## Testing Near Equality
>
> Assertions are also helpful for catching abnormal behaviors, such as those
> that arise with floating point arithmetic. Using the assert keyword, how could
> you test whether some value is almost the same as another value?
>
> - My package, mynum, provides the number a.
> - Use the `assert` keyword to check whether the number a is greater than 2.
> - Use the `assert` keyword to check that a is equal to 2 within an error of 0.003.
>
> ~~~
> from mynum import a
> # greater than 2 assertion here
> # 0.003 assertion here
> ~~~
> {: .python}
>
> > ## Solution
> > The simplest solution is
> > ~~~
> > from mynum import a
> > assert a > 2
> > assert (2 - 0.003) < a < (2 + 0.003)
> > ~~~
> > {: .python}
> > However what if we make a typo in the second assertion? We could be testing
> > for the wrong thing! A better solution would be to use the function
> > `assert_allclose` from NumPy:
> > ~~~
> > from numpy.testing import assert_allclose
> > from mynum import a
> > assert a > 2
> > assert_allclose(a, 2, atol=0.003, rtol=0)
> > ~~~
> > {: .python}
> {: .solution}
{: .challenge}

> ## Tools to use: `numpy.testing`
> The NumPy numerical computing library has a module which provides functions
> for testing floating point numbers, [`numpy.testing`](https://docs.scipy.org/doc/numpy/reference/routines.testing.html),
> which you should use whenever you are dealing with floating point numbers.
{: .testimonial}

