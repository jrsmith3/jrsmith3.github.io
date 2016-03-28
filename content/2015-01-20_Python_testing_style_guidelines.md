Title: Python testing style guidelines
Date: 2015-01-20
Category: Blog
Author: Joshua Ryan Smith
Summary: Some notes on what I've learned about structuring python tests

This post was updated on 2016-03-26 and is revision 0.2.0.

Testing a module you've written in python can easily spiral out of control, particularly one which simulates a physical phenomenon. You want to be sure that your tests are comprehensive, but its quite easy to fall down a rabbit hole of writing completely inane tests for edge cases that will never occur in practice. After writing and refactoring my [`tec`](https://github.com/jrsmith3/tec) module several times, I've gotten a sense of the various types of tests that need to be written and how to organize them. I intend this strategy to be along the lines of a "style guidelines" type document like [pep8](https://www.python.org/dev/peps/pep-0008/) or [Strunk and White](https://en.wikipedia.org/wiki/The_Elements_of_Style).

These guidelines assume the reader has an understanding of testing python code and the `unittest` framework. Resources for testing code with Python are given at the end of this essay.


Overview
========
Tests tend to fall into two categories: API tests and numerical tests. API tests test the interface of the module: ensuring proper return types of methods, ensuring exceptions are raised under appropriate conditions, methods that return units actually return the correct unit, etc. Numerical tests verify the numerical accuracy of the methods that return the results of the physical simulation.


Test style guidelines
=====================
Organization of test files in the filesystem
--------------------------------------------
Files containing tests for a python module should be located in a `test` directory in the root of the repo [for the sake of separation of concerns](http://pytest.org/latest/goodpractices.html?highlight=inline#choosing-a-test-layout-import-rules). Each file in the `test` directory should contain tests for one and only one class/function defined in the module. Files containing tests should be named according to the rubric 

    test_ClassName.py

At the top of the file containing the tests, I have the typical python import statements. Below that, I usually define some common initialization parameters needed for the tests. These initialization parameters aren't used directly in the tests as I will explain momentarily.

Next, I usually define a base class (called `Base`) which is a subclass of `unittest.TestCase`. In this class I define a `setUp` method (and possibly `tearDown`) because I've found that most of the tests I write require a common environment in which to be run. This base class allows me to split up tests according to functionality by writing subclasses of `Base` while avoiding copying/pasting the same `setUp` method between these classes. Therefore I avoid introducing the category of bugs associated with copying and pasting code for reuse. In this `Base` class I `copy` the common initialization parameters I've defined at the top of the test file into attributes of the class so that a uniform environment is created for each test via `Base.setUp`. I've found there's a category of bugs that appear if I directly use the initialization parameters defined at the top of the test file: some tests require the initialization parameters to be changed slightly. Its possible to define a parameter and have it change in memory as a result of a test. Subsequent tests will therefore throw errors.

I organize tests according to functionality by subclassing `Base` (and thus `unittest.TestCase`). For classes that implement a physical phenomenon, tests tend to fall into the following categories:

* `Instantiation` - test all aspects of instantiating an object. Includes input of wrong type, input outside of a bound, etc.
* `Set` - test all aspects of instantiating an object. Includes similar tests to the instantiation tests.
* `MethodsInput` - test methods that take input. Includes passing data of the wrong type, data that's outside of a constraint, etc.
* `MethodsReturnType` - test that methods return the proper type of data.
* `MethodsReturnUnits` - test that methods return the proper units, assuming the method returns data of type `astropy.units.Unit`.
* `MethodsReturnValues` - tests that the methods return appropriate values. These tests are particularly important for methods which actually implement the physical model; I call them "calculator methods."

Each class contains methods that implement a test. These methods are named according to the rubric

    test_name_condition

Where "name" refers to the name of the attribute or method being tested and "condition" refers to the condition being tested. The name of the class which contains the attribute or method under test should not appear in the test method name, nor should the functionality of the test. For example, the following test names should not be used:

    test_ClassName_temp_less_than_zero
    test_input_temp_less_than_zero

The first example contains the name of the class (`ClassName`) in which the attribute `temp` is found. The class name is superfluous in this case because this test is presumably located in a file named `test_ClassName.py`.

The second example contains the name of the functionality being tested, i.e. "input." The functionality in this case is superfluous because the test is presumably a method of the class `Instantiation`
.

Docstrings should include the name of the class under test, the functionality being tested, the name of the attribute/method being tested, and the condition being tested. In this way, `nosetests` will display all the information required to locate the failed/erronious test within this classification scheme. The order is not strictly required; sometimes it is easier to write something like "Setting ClassName.strictly_positive_attribute < 0 is invalid." The docstrings should describe logically what the test method does. In other words, it should be clear from the docstring how the test is passed or failed.


Additional style guidelines for numerical tests
-----------------------------------------------
Each calculator method requires the following items in order to be considered to be fully tested.

* A unit listed in its docstring. No mention of unit indicates the quantity is unitless.
* A value of uncertainty listed in its docstring.
* An analysis of the method's uncertainty documented in that method's test's docstring.
* A unit test that tests the method against standard data.
* Additional unit tests that test any other special, edge, or corner cases.


Remarks
-------
If a class has several methods that do similar things, many of the tests in a class will be very similar. I wrote a really dumb command line tool I call [`testwriter`](https://github.com/jrsmith3/testwriter) to automatically generate code for these tests.


Example/skeleton test file
==========================

```python
# -*- coding: utf-8 -*-
# Import statements
# =================

# Common input parameters
# =======================

# Base classes
# ============
class Base(unittest.TestCase):
    """
    Base class for tests

    This class defines a common `setUp` method that defines attributes which are used in the various tests.
    """
    def setUp(self):
        pass


# Test classes
# ============
class Instantiation(Base):
    """
    Tests all aspects of instantiation

    Tests include: instantiation with args of wrong type, instantiation with input values outside constraints, etc.
    """
    pass

    # Input arguments wrong type
    # ==========================

    # Input arguments outside constraints
    # ===================================


class Set(Base):
    """
    Tests all aspects of setting attributes

    Tests include: setting attributes of wrong type, setting attributes outside their constraints, etc.
    """
    pass

    # Set attribute wrong type
    # ========================

    # Set attribute outside constraint
    # ================================


class MethodsInput(Base):
    """
    Tests methods which take input parameters

    Tests include: passing invalid input, etc.
    """
    pass


class MethodsReturnType(Base):
    """
    Tests methods' output types
    """
    pass


class MethodsReturnUnits(Base):
    """
    Tests methods' output units where applicable
    """
    pass
    

class MethodsReturnValues(Base):
    """
    Tests values of methods against known values
    """
    pass
```


Resources
=========
[Arbuckle (isbn 9781847198846)](http://www.amazon.com/dp/1847198848) gives an excellent, but slightly dated, introduction to python testing.
The [Python testing blog](http://pythontesting.net).
The [py.test module](http://pytest.org/latest/).
