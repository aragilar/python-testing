---
title: Using Exceptions
teaching: 10
exercises: 0
questions:
- "How do I handle unusual behavior while the code runs?"
objectives:
- "Understand that exceptions are effectively specialized runtime tests"
- "Learn when to use exceptions and what exceptions are available"
keypoints:
- "Exceptions are effectively specialized runtime tests"
- "Exceptions can be caught and handled with a try-except block"
- "Many built-in Exception types are available"
---

We previously covered what an exception is; now we will learn how to control
exceptions, and create our own.

## `try-except` statements

Sometimes we can't control everything, and an exception is raised (e.g. we try
to download a file which doesn't exist). Python gives us the tool to handle
these exceptions called `try-except` statements. `try-except` are similar to
`if-else` statements: they are based on conditions (in this case the type of
exception), can have multiple parts, and require the use of a colon (`:`).

`try-except` statements have the following structure:
~~~
try:
    # some code which may raise an exception
except SomeTypeOfException as error:
    # handle the exception
except SomeOtherTypeOfException as different_error:
    # handle the different exception
else:
    # there was no exception
finally:
    # code that always gets called, used for cleanup
~~~
{: .language-python}

Note that both `else` and `finally` are optional, you will see many cases where
neither is used.

For a example of how to use `try-except` statments, let's look at calculating
the mean of a list in python:
~~~
def mean(num_list):
    return sum(num_list)/len(num_list)
~~~
{: .language-python}

We can pass our new mean function a list, and we can see it works:
~~~
mean([1,2])
~~~
{: .language-python}
~~~
1.5
~~~
{: .output}
but what happens when we give it an empty list:
~~~
mean([])
~~~
{: .language-python}
~~~
Traceback (most recent call last):
    ...
ZeroDivisionError: division by zero
~~~
{: .error}
Since the length of the list was `0`, we divided by zero and Python raised an
exception. We should add some code to our mean function which handles the empty
list case. We'll assume for simplicity's sake that 0 is going to be the value
returned for the empty list case, and avoid discussions of correctness for now.

Using the `try-except` statement to handle the `ZeroDivisionError`, our new mean
function becomes:
~~~
def mean(num_list):
    try:
        return sum(num_list)/len(num_list)
    except ZeroDivisionError:
        return 0
~~~
{: .language-python}
~~~
mean([])
~~~
{: .language-python}
~~~
0
~~~
{: .output}

We could also check that the list is not empty before trying to compute the
mean:
~~~
def mean(num_list):
    if len(num_list) == 0:
        return 0
    return sum(num_list)/len(num_list)
~~~
{: .language-python}

Checking a condition to avoid an exception being raised has the name "look
before you leap" (LBYL), whereas catching the exception has the name "easier to
ask forgiveness than permission" (EAFP). Choosing whether to check or catch the
exception requires judgement which can only be gained through experience:
usually it is better to simply catch the exception, as there are numerous bugs
which are caused by choosing to check over catching the exception. However, this
assumes an exception will be raised, rather than an incorrect value being
returned. The best thing to do when unsure is to read the documentation (whether
it is Python's documentation, or a library that you are using), and see what if
any exceptions are raised by the functions you are using, and if the
documentation is unclear, ask the developers or post a question on [stack
exchange](https://stackoverflow.com/questions/tagged/python).

## Raising exceptions
We've covered how to catch exceptions, now we're going to raise them ourselves.
We've hinted at this previously, but the keyword to raise an exception is
`raise`. For our mean function, this means we can return an exception rather
than 0 for an empty list:
~~~
def mean(num_list):
    if len(num_list) == 0 :
      raise ValueError("The algebraic mean of an empty list is undefined. "
                       "Please provide a list of numbers")
    else :
      return sum(num_list)/len(num_list)
~~~
{: .language-python}

Remember that once an exception is raised, it will be passed upward in the
program scope, and potentially cause the program to exit early. If there's an
problem which may cause issues later in the program, it's nearly always better
to raise an exception rather than waiting for the issue to become a major
problem—this is known as "failing fast".

Exceptions can be raised inside `try-except` statements, as shown with the
following example:
We have our mean function, but what happens when we give it a list if strings
rather than a list of numbers?
~~~
mean(["abc"])
~~~
{: .language-python}
~~~
Traceback (most recent call last):
    ...
TypeError: unsupported operand type(s) for +: 'int' and 'str'
~~~
{: .error}
so we need to handle the `TypeError` in our mean function:
~~~
def mean(num_list):
    try:
        return sum(num_list)/len(num_list)
    except ZeroDivisionError:
        return 0
    except TypeError:
        raise TypeError("The algebraic mean of an non-numerical list is "
                        "undefined. Please provide a list of numbers.")
~~~
{: .language-python}
This allows us to provide a more specific and useful error message.


> ## What else could go wrong?
>
> 1. Think of some other type of exception that could be raised by the try 
> block.
> 2. Guard against it by adding an except clause.
> 3. Use the mean function in three different ways, so that you cause each
> exceptional case.
{: .callout}

Exceptions have the advantage of being simple to include and powerfully helpful
to the user. However, not all behaviors can or should be found with runtime
exceptions. Most behaviors should be validated with unit tests.

> ## Tips: Do's and Don'ts of exceptions
> * **Don't** use more general exceptions either when raising or catching
>   exceptions—be as specific as you can.
> * **Do** reraise exceptions when you are unable to solve the issue—hiding
>   internal details only makes debugging harder.
> * **Do** try to keep the code inside the `try` portion as small as
>   possible—this reduces the chance of capturing an exception that is different
>   to the one expected.
> * **Do** "fail fast"—if there's a problem, raise an exception as soon as
>   possible.
> * **Do** think about whether to check or catch the exception.
{: .testimonial}

