---
title: Exceptions, Status Values and Tracebacks
teaching: 10
exercises: 0
questions:
- "What does this error mean?"
objectives:
- "Learn how to read a Python traceback."
- "Learn what an exception is, and that there are many types."
keypoints:
- "Exceptions are one way exposing errors."
- "There are many types of exceptions."
---

When a computer tries to perform some problematic task (e.g. try to run a python
file with a typographic error, divide by zero), the computer needs to inform the
program (and by extension the programmer and user) of the problem. In Python,
this means either:

1. Returning a value to indicate an error occurred. This value is called a
   status value, a flag, or an error code. Status values you may have seen
   before include `inf` and `NaN`, and `None` is a commonly used status value in
   Python.
2. "Raising" an exception. The word "raising" in this context refers to how the
   exception is created and passed around by Python. Other languages may use the
   terminology "thrown".

In general, Python programmers prefer using exceptions over status values, and
we will provide some general guidance about when to use both in the next
chapter.

You have likely seen quite a few different types of exceptions, for example the
`SyntaxError`, which pops up when you've made a typographic error, or a
`NameError` when you have misspelt a variable name. You may have also seen
`ZeroDivisionError` when accidentally dividing by 0, or `FileNotFoundError` when
you have misspelt a file name. All these different exceptions have two things in
common: a message which tells you (sometimes cryptically) what went wrong, and
where is occurred. Learning to debug Python is learning to use those two bits of
information to find out why there is a problem.

> ## What different types of exceptions are there?
> A list of different exceptions included with Python can be found at
> https://docs.python.org/3/library/exceptions.html#exception-hierarchy.
> In addition, it is possible to create custom exceptions (which we won't cover
> in this course).
{: .callout}

Below are three Python functions which call each other in a Python file that
I've called `divide_by_zero.py`.

~~~
def inner():
    return 1/0

def outer():
    return inner()

def more_outer():
    return outer()

more_outer()
~~~
{: .language-python}

and if we run the python file:

~~~
$ python divide_by_zero.py
~~~
{: .language-shell}

we get the following output:

~~~
Traceback (most recent call last):
  File "divide_by_zero.py", line 10, in <module>
    more_outer()
  File "divide_by_zero.py", line 8, in more_outer
    return outer()
  File "divide_by_zero.py", line 5, in outer
    return inner()
  File "divide_by_zero.py", line 2, in inner
    return 1/0
ZeroDivisionError: integer division or modulo by zero
~~~
{: .error}

This output is called a traceback (or backtrace or stacktrace): the traceback
contains the exception that was raised, where it was raised originally (line
number, name of function and filename, where the function which called the
function which raised the exception, and so on until we're out of functions we
called. Read tracebacks from bottom to top; the bottom is the location of the
raised exception.

In the above example, the line

~~~
ZeroDivisionError: integer division or modulo by zero
~~~
{: .error}

tells us the exception name and a description of the problem (we tried to divide
by zero); the lines

~~~
  File "divide_by_zero.py", line 2, in inner
    return 1/0
~~~
{: .error}

tell where the exception was raised (line 2 of `divide_by_zero.py`, inside the
function called `inner`).

Whilst in our simple case we can directly see the problem, remember that just
because an exception was raised on a particular line, it does not mean that the
bug occurs on that line. Debugging is a skill which takes practice and
experience to develop, and best way of improving it is to notice patterns in the
types of bugs that you run into. Helping others with debugging their code is
another way to gain experience.

> ## Tools to use: Debuggers
> Debuggers are a group of programs which allow users to step through the
> running of a program. Many text editors and programming environments provide
> either their own debugger, or build around a debugger. Python has a built in
> debugger [pdb](https://docs.python.org/3/library/pdb.html): usually it's
> better to learn to use pdb than to add lots of print statements.
{: .testimonial}
