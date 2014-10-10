# Python Style Guide

So you wanna write Python code for SOON_ eh? Great, just follow these simple style guides for Python. It's important we all sing from the same hymn sheet to ensure our code looks nice but also means we aren't faced with context switching how we read code from project to project.

Python already has a community driven style guide called PEP8 and you should read it because the foundation of these guides of from PEP8, you can read it here: http://www.python.org/dev/peps/pep-0008/

The big things from PEP8 are:

 * Space indentation
 * Indentation to 4 spaces
 * Line length guide of no more than 80 chars

But we also have some good ideas about how we can make it a little be better.

## Imports

### Single line imports

Lets start at the top, imports should be broken down onto several lines, so no single line imports separated by commas.

``` python
import os, sys
```

Should be:

``` python
import os
import sys
```

### Import as

Try not to rename imports using the `as` keyword, it can get a bit messy and confusing and make code hard to follow:

``` python
from os.path import basename, dirname, join as os_join
from other.module.foo import join as foo_join
```

In these situations its best to just import the os module and be explicit in the code, for example.

```
import os
os.path.join(...)  # more explicit than just join()
```

### Organisation

Keep your imports organised alphabetically and separate `from` and `import` keywords, for example:

``` python
from __future__ import division
import signal
import sys
import multiprocessing
import requests
from dateutil.parser import parse
from dateutil.tz import tzutc
import datetime
import pickle
import time
import logging
from safelogger import getMultiProcessLogger
```

This is hard to read and difficult to find specific imports, it would be much better like this:

``` python
from __future__ import division

import datetime
import logging
import multiprocessing
import pickle
import requests
import signal
import sys
import time

from dateutil.parser import parse
from dateutil.tz import tzutc
from safelogger import getMultiProcessLogger
```

### Don't leave dead imports hanging around

If you have imported something and no longer use it remove it from the imports at the top of the file. Dead imports make things messy.

## Strings

### Doubles or Singles

In python string interpolation can be done with both single and double quotes so it really makes no difference to which you use, its purely a stylistic choice, however the python community has leaned towards single quotes due to its quicker typing abilities and clean look.

There for all strings should be single (') quotes.

``` python
with open('some/path/foo.txt', 'r') as f
    ...
```

### Doc Strings

There is a PEP for doc strings: [PEP 257](http://legacy.python.org/dev/peps/pep-0257/). In short doubles are used:

``` python
def foo():
    """
    My fancy func
    """

    return 'fancy'
```

### String formatting

When formatting strings, always use the format method. To avoid issues on Python 2.6 and older environments, and to make code as explicit as possible, always define indices or identifiers. For example:

``` python
foo = 'Hello {}'.format('world')
```

For explicitly should be:

``` python
foo = 'Hello {0}'.format('world')
# or
foo = 'Hello {message}'.format(message='world')
```

### Indentation

As PEP8 prescribes that line lengths should not be greater than 79 characters, indentation can become quite important. Especially so when you have long function definitions or are chaining function calls.

Both of these examples below are not cool:

``` python
def my_long_function(
    arg1
    arg2='foo',
    arg3='bar'):

    pass

def my_long_function(arg1, arg2='foo',
                     arg3='bar'):
    pass
```

Good (Note it’s double indented which allows you to see separation from function code):

``` python
def my_long_function(
        arg1
        arg2='foo',
        arg3='bar'):

    pass

# This is also ok but not so pretty
def my_long_function(arg1
                     arg2='foo',
                     arg3='bar'):
    pass
```

### Vertical Code > Horizontal Code

Code is more difficult to read horizontally than vertically, so it's better for code to long long than wide, for example:

``` python
    affiliate_id = db.Column(db.Integer, db.ForeignKey('organisation.id'),
                             nullable=True)
```

The above example is totally valid from a PEP008 stand point but this looks nicer and is easier to pick out each argument:

``` python
    affiliate_id = db.Column(
        db.Integer,
        db.ForeignKey('organisation.id'),
        nullable=True)
```

This isn't a hard rule however but use your disgression.

## Docstrings and Comments

A docstring should come directly after a function definition and should contain a brief description of what the function does, any arguments the function takes and the type of returned data if applicable. Docstrings should use double quotes and start on new lines.

Comments should start with a hash (#) and should be limited to a single line if possible.

Also check out PEP 257.

We use a Sphinx plugin called Napoleon which allows you to write cleaner doc strings for auto documentation generation. Read about it [here](http://sphinxcontrib-napoleon.readthedocs.org/en/latest/).

``` python
def foo(arg1, arg2=False):
    """
    Augments arguments to return foobar'ed values.

	Arguments
	---------
    arg1: str
    	The first value to augment

    arg2: bool
    	Do really bad things -- Default False

	Returns
	-------
    str
    	A string thats been made cool yo!
    """

    do_pythony_things()  # Inline comments, 2 spaces away

    # This does cool things
    foo = cool_things()

    return foo
```

### File Docstrings

It’s a good idea to add a docstring to the top of the file. This can contain a brief synopsis of the module and the python dotted path for importing this module, usage example may also be good.

``` python
"""
thing.foo.bar
=============

Does some cool things with python.
"""


def foo():
    pass
```

### File Encoding: PEP 263

This particular PEP is regarding always setting file encodings at the top of files. This is good practice and something we should comply to for all the reasons lined out [here](http://legacy.python.org/dev/peps/pep-0263/). In general we should always encode to UTF-8 unless there is a good reason not too.

``` python
#!/usr/bin/env python
# encoding: utf-8

"""
thing.foo.bar
=============

Does some cool things with python.
"""

def foo():
    pass
```

## Dependency Management

One of myths with python is you need to manage your dependencies with a `requirements.txt` which you use with `pip install`. This is all lies! Python has long had support for dependency resolution in `setup.py`.

### `setup.py`

The `setup.py` files support the `install_requires` keyword argument which takes a list of package names, lets take this simple example of a `setup.py`:

``` python
from setuptools import setup, find_packages

# Setup function
setup(
    name='foo',
    version='0.0.1',
    author='SOON_',
    author_email='dorks@thisissoon.com',
    url='http://thisissoon.com',
    description='Foo things are foo',
    long_description='More foo things are goo',
    packages=find_packages(
        exclude=[
            "tests"]),
    include_package_data=True,
    zip_safe=False,
    # Dependencies
    install_requires=[
        'django==1.5',
        'requests'
    ],
    # Dependencies not hosted on PyPi
    dependency_links=[],
```

When you run `python setup.py install||develop` the dependencies will also be installed. This means our python module can also be uploaded to a package index server and could be installed using pip. This stream lines our deployment process for python because we don't have to manage dependencies separately from the installation of the package.

Dependencies for different environments should also be encouraged, production environments don't need `ipython` or `nose` or other testing / development utilities, so lets break these out:

``` python
from setuptools import setup, find_packages

# Setup function
setup(
    name='foo',
    version='0.0.1',
    author='SOON_',
    author_email='dorks@thisissoon.com',
    url='http://thisissoon.com',
    description='Foo things are foo',
    long_description='More foo things are goo',
    packages=find_packages(
        exclude=[
            "tests"]),
    include_package_data=True,
    zip_safe=False,
    # Dependencies
    install_requires=[
        'django==1.5',
        'requests'
    ],
    extras_require={
        'test': [
             'nose',
         ],
        'develop': [
             'nose',
             'ipython',
         ],
    # Dependencies not hosted on PyPi
    dependency_links=[],
```

So here we have used the `extras_require` key word argument to provide a dictionary of lists containing our dependencies. We can install these using pip:

```
pip install -e .[develop]
```

or

```
pip install -e .[test]
```

In both examples the python module will be installed as a development egg (`setup.py develop`), install the dependencies and extra dependencies.

### Make

A `Makefile` can be used to help make this easier. `Makefile`'s are great because they don't require anything extra installed on a system to work like fabric tasks, an example `Makefile`:

``` make
install:
	bash -c 'pip install -e .'

develop:
	bash -c 'pip install -e .[develop]'

test:
	bash -c 'pip install -e .[test]'
	python setup.py test
```

We've used `bash -c` to ensure this command works in all shells, for example ZSH and FISH. We can use this by running:

```
make develop
```

or

```
make test
```

or

```
make install
```

### Reading Dependencies from files

It still might be a good idea to keep dependencies in a file for sanity, in our `setup.py` we can have a function which reads these files and builds a list:

``` python
def read_dependencies(filename):
    """ Read requirements file and process them into a list
    for usage in the setup method.

	Arguments
	---------
	filename : str
		Path to the file to read line by line

	Returns
	-------
	list:
		List of requirements
    """

    dependencies = []
    with open(filename) as f:
        for line in f.readlines():
            if not line or line.startswith('#'):
                continue
            dependencies.append(line.strip())
    return dependencies
```

We also need to get the paths to the files:

``` python
# Get current directory where setup is running
try:
    SETUP_DIRNAME = os.path.dirname(__file__)
except NameError:
    SETUP_DIRNAME = os.path.dirname(sys.argv[0])

# Change directory
if SETUP_DIRNAME != '':
    os.chdir(SETUP_DIRNAME)

# Paths to requirement files
INSTALL_DEPS = os.path.join('dependencies', 'install.txt')
TEST_DEPS = os.path.join('dependencies', 'test.txt')
DEV_DEPS = os.path.join('dependencies', 'dev.txt')
```

This means our `install_requires` and `extra_requires` can now look like this:

``` python
    install_requires=read_dependencies(INSTALL_DEPS),
    extras_require={
        'test': read_dependencies(TEST_DEPS),
        'develop': read_dependencies(DEV_DEPS)},
```

The above assumes there is a directory called `dependencies` in the root of the project which contains 3 files:

 * install.txt
 * test.txt
 * dev.txt

These contain dependancies on each line like a standard pip requirements file.

## Testing Frameworks

When writing unit tests or integration tests or any kind of test at all consider the what code you're actually testing and what the requirements are. When possible do not actually write to the database as this adds extra time to tests, we don't need to test weather rows are created or not we just need to test that the code that would add the row is called so mock the database call.

The official Python 3 testing framework now includes Mock by default so please use Mock for all your mocking and patching needs.

The following are approved frameworks which we should use for testing:

 * Pytest (and plugins): http://pytest.org/latest/
 * Mock: http://www.voidspace.org.uk/python/mock/
 * Factory Boy: http://factoryboy.readthedocs.org/en/latest/
