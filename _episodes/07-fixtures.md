---
title: Fixtures
teaching: 10
exercises: 0
questions:
- "How do I create and cleanup the data I need to test the code?"
objectives:
- Understand how test fixtures can help write tests.
keypoints:
-   It may be necessary to set up "fixtures" composing the test environment.
---

## Setup and Teardown

The above example didn't require much setup or teardown. Consider, however, the
following example that could arise when communicating with third-party programs.
You have a function `f()` which will write a file named `yes.txt` to disk with
the value 42 but only if a file `no.txt` does not exist. To truly test that the
function works, you would want to ensure that neither `yes.txt` nor `no.txt`
existed before you ran your test. After the test, you want to clean up after
yourself before the next test comes along.  You could write the test, setup,
and teardown functions as follows:

~~~
import os

from mod import f

def f_setup():
    # The f_setup() function tests ensure that neither the yes.txt nor the
    # no.txt files exist.
    files = os.listdir('.')
    if 'no.txt' in files:
        os.remove('no.txt')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def f_teardown():
    # The f_teardown() function removes the yes.txt file, if it was created.
    files = os.listdir('.')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def test_f():
    # The first action of test_f() is to make sure the file system is clean.
    f_setup()
    exp = 42
    f()
    with open('yes.txt', 'r') as fhandle:
        obs = int(fhandle.read())
    assert obs == exp
    # The last action of test_f() is to clean up after itself.
    f_teardown()
~~~
{: .python}

The above implementation of setup and teardown is usually fine.
However, it does not guarantee that the `f_setup()` and the `f_teardown()` 
functions will be called. This is because an unexpected error anywhere in 
the body of `f()` or `test_f()` will cause the test to abort before the 
teardown function is reached.

These setup and teardown behaviors are needed when _test fixtures_
must be created.  A fixture is any environmental state or object that
is required for the test to successfully run.

As above, a function that is executed before the test to prepare the fixture
is called a _setup_ function. One that is executed to mop-up side effects
after a test is run is called a _teardown_ function.
By giving our setup and teardown functions special names pytest will
ensure that they are run before and after our test function regardless of
what happens in the test function.
Those special names are `setup_function` and `teardown_function`,
and each needs to take a single argument: the test function being run
(in this case we will not use the argument).

~~~
import os

from mod import f

def setup_function(func):
    # The setup_function() function tests ensure that neither the yes.txt nor the
    # no.txt files exist.
    files = os.listdir('.')
    if 'no.txt' in files:
        os.remove('no.txt')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def teardown_function(func):
    # The f_teardown() function removes the yes.txt file, if it was created.
    files = os.listdir('.')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def test_f():
    exp = 42
    f()
    with open('yes.txt', 'r') as fhandle:
        obs = int(fhandle.read())
    assert obs == exp
~~~
{: .python}

The setup and teardown functions make our test simpler and the teardown
function is guaranteed to be run even if an exception happens in our test.
In addition, the setup and teardown functions will be automatically called for
_every_ test in a given file so that each begins and ends with clean state.

## Pytest Fixtures
Currently these tests are creating and removing files in the current directory:
while it's unlikely you have a `yes.txt` or `no.txt` in your current directory,
tests should not assume that they can change files in the current directory.
What we should do is use a temporary directory. Python provides a module called
[`tempfile`](https://docs.python.org/3/library/tempfile.html), which pytest has
nicely integrated into their testing system so that we can get a new temporary
(and empty) directory for each test.

~~~
from mod import f

def test_f(tmpdir):
    exp = 42
    f(tmpdir)
    with open(tmpdir.join('yes.txt'), 'r') as fhandle:
        obs = int(fhandle.read())
    assert obs == exp
~~~
{: .python}

Note that we've only tested when there is no `no.txt` file. Let's add that test:
~~~
import os.path

from mod import f

def test_f(tmpdir):
    exp = 42
    f(tmpdir)
    with open(tmpdir.join('yes.txt'), 'r') as fhandle:
        obs = int(fhandle.read())
    assert obs == exp

def test_f_with_no(tmpdir):
    with open(tmpdir.join('no.txt'), 'x'):
        pass
    f(tmpdir)
    assert not os.path.exists(tmpdir.join('yes.txt'))
~~~
{: .python}

We can also create our own pytest fixtures, but we're going to need to learn
about some new Python syntax.

> ## Decorators
> You may have seen the following bit of python code before
> ~~~
> @something
> def something_else():
>     pass
> ~~~
> {: .python}
> The `@something` is called a decorator, and this decorator is modifying the
> function below it (in this case `something_else`. pytest uses decorators
> widely, e.g. you can tell pytest to skip a test with the `pytest.mark.skip`
> decorator. See
> [this stackexchange answer](https://stackoverflow.com/questions/739654/how-to-make-a-chain-of-function-decorators/1594484#1594484)
> for further information about how decorators work in Python.
{: .callout}

To create a fixture which ensures we have a `no.txt`, we need to import pytest,
and then use the `pytest.fixture` decorator
~~~
import os.path

import pytest

from mod import f

@pytest.fixture
def no_txt_dir(tmpdir):
    with open(tmpdir.join('no.txt'), 'x'):
        pass
    return tmpdir

def test_f(tmpdir):
    exp = 42
    f(tmpdir)
    with open(tmpdir.join('yes.txt'), 'r') as fhandle:
        obs = int(fhandle.read())
    assert obs == exp

def test_f_with_no(no_txt_dir):
    f(no_txt_dir)
    assert not os.path.exists(no_txt_dir.join('yes.txt'))
~~~
{: .python}
