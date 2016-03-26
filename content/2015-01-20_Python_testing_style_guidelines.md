Title: Python testing style guidelines
Date: 2015-01-20
Category: Blog
Author: Joshua Ryan Smith
Summary: Some notes on what I've learned about structuring python tests

<!-- 
The structure of this description should be: 
* Numerics
* API
* Style guidelines 
-->

Testing a module you've written in python can easily spiral out of control, particularly one which simulates a physical phenomenon. You want to be sure that your tests are comprehensive, but its quite easy to fall down a rabbit hole of writing completely inane tests for edge cases that will never occur in practice. After writing and refactoring my [`tec`](https://github.com/jrsmith3/tec) module several times, I've gotten a sense of the various types of tests that need to be written and how to organize them. This post is the 0.2.0 version of the strategy I'm using to test `tec`. I intend this strategy to be along the lines of a "style guidelines" type document like [pep8](https://www.python.org/dev/peps/pep-0008/) or [Strunk and White](https://en.wikipedia.org/wiki/The_Elements_of_Style).

These guidelines assume the reader has an understanding of testing python code and the `unittest` framework. Resources for understanding testing with Python are given at the end of this essay.


Overview
========
Tests tend to fall into two categories: API tests and numerical tests. API tests test the interface of the module: ensuring proper return types of methods, ensuring exceptions are raised under appropriate conditions, methods that return units actually return the correct unit, etc. Numerical tests verify the numerical accuracy of the methods that return the results of the physical simulation.

I've found that writing API tests tend to pose trickier programming problems than numerical tests, but performing the analysis to determine the correctness of a numerical output can be quite challenging.

You are probably reading this essay to learn how to determine if your calculator methods are returning the correct results. The short answer is: the precision of the physical constants is the limiting factor for your calculator methods. The longer answer is: the accuracy of the numerical results depends on the correct implementation of the model; the precision is determined by the algorithm used to calculate the result and the precision of the inputs (which includes physical constants) to the algorithm.

Basic numerical testing strategy
----------
Soon I will get to the details of how to ensure your algorithms and understand uncertainty propagation, but first I want to discuss the basic strategy for testing calculator methods. The good news is that the testing strategy is very simple: compare the output of each method to a standard set of data. The standard set of data is simply a list of inputs required by the calculator method and the corresponding (accurate and precise) output value of the method.

As a very simple example, say you wrote a method to calculate...


Style guidelines for tests for python classes simulating physical phenomnea
===========================================================================
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


Remarks
-------
If a class has several methods that do similar things, many of the tests in a class will be very similar. I wrote a really dumb command line tool I call [`testwriter`](https://github.com/jrsmith3/testwriter) to automatically generate code for these tests.


Numerical testing
=================
Numerical tests evaluate the accuracy of the output of methods that return a quantitative value and provide assurance that calculations are being performed properly. The strategy for numerical testing is to evaluate the result of a calculator method against a known standard value and ensure the two values match to within an acceptable uncertainty.

This entire testing strategy is based on the assumption that the computer can accurately and repeatably perform simple calculations. I assume that as far as the computer is concerned, the product

    2 * 5
  
is just as easy to calculate as the product

    2.333690544228055 * 5.44073192976832565

and the result is just as accurate. Most calculator methods are straightforward arithmetical operations, and the important task is to analyze the uncertainty propagation.

For every calculator method there is a tremendous number of combinations of input paramters. The problem isn't that it would take an unacceptably long time with unacceptably large computing resources to exhaustively calculate all of those combinations. The problem is that there's no reasonable way to check the resulting set of data.

Since checking a comprehensive standard set of data is unreasonable, the next approach would be to have a much smaller set of standard data which is a subset of the comprehensive set of standard data. The question now becomes: how small does the subset need to be? If the subset gets too big, the problem of reasonably checking all the values re-emerges.

Since we've already assumed that the computer accurately and repeatably calculates arithmatic operations, the subset of standard data can be quite small. Given that the human element is the limiting factor in terms of ease of performing a numerical calculation,  it is best to choose special case parameters so that humans can quickly spot-check all of the standard data.

The foundation of this testing strategy is to check the uncertainty propagation of each algorithm to get a picture of the uncertainty of compound algorithms. The strategy of checking algorithms is much better than slavishly checking sets of standard data.

The typical strategy to test a calculator method has the following components. First, the uncertainty of the algorithm implemented by the calculator method is analyzed using uncertainty propagation theory (9780935702422). This analysis will appear in the docstring of the unit test which performs the numerical of that calculator method. Code will be written that generates easily checked-by-humans data. This code is used to generate the standard set of data for the calculator method. The standard set of data is generated over a range of input values which are reasonable (i.e. temperature will never approach 1e9K in a semiconductor simulation). The numerical accuracy unit test for the calculator method under test will compare the output of the calculator method to the (verified) output of the standard data. Each calculator method will usually only have one numerical accuracy unit test.

Each calculator method requires the following items in order to be considered to be fully tested.

* A unit listed in its docstring.
* A value of uncertainty listed in its docstring.
* An analysis of the method's uncertainty documented in that method's test's docstring.
* A standard set of data, typically a set of special cases; file naming convention: Class.method_name_STANDARD.dat
* A script that generates the standard data; file naming convention: Class.method_name_STANDARD.py
* A unit test that tests the method against the standard data.
* Additional unit tests that test any other special, edge, or corner cases.
  
Distinguishment between classification of various cases is important in this discussion. "Special case" refers to a case that gives a mathematically unique result, specifically an integer power of 10 within the precision of the calculation. For example, some combination of parameters may yield an output current density of precisely 10 A cm^{-2}. "Edge case" refers to the situation where one parameter is pushed to its limit. "Corner case" refers to the situation where more than one parameter is at an extreme but allowed value and where errors might occur.

Uncertainty analysis, uncertainty quantification, and units
-----------------------------------------------------------
The majority of the calculator methods in the tec package are straightforward mathematical operations like multiplication, division, addition, subtraction, and things like exp(). These calculator methods can be thought of as very simple algorithms; they are just a one-line calculation. I can very easily evaluate the uncertainty propagation through these calculators and determine what it means to pass these tests. Subclasses of the TEC class will generally only overload methods related to calculating the motive, not methods that calculate the output current density, output voltage, etc. Uncertainty in the motive calculation algorithm will typically enter these other methods as a single value. The result is that the output of the non-motive calculators may be numerically inaccurate, but the calculator algorithm is still returning values that are acceptably precise. In other words, the simple calculator methods may be precisely calculating the wrong answer.

The point is that I can write numerical tests for all of the methods in the base TEC class. If the simple calculators pass the tests, I can rest assured that changing  the motive calculator won't affect the precision of these simple calculator methods. Therefore, child classes only require evaluation and testing of the method used to calculate the motive, not any simple calculator method.

In the calculator methods there are three sources of uncertainty. One, the machine isn't perfectly precise: I'm pretty sure that all the numbers are kept to double precision. In other words, the machine can only approximate irrational numbers like \pi and sqrt(2). Therefore, the machine propagates uncertainty through calculations and any number which is a result of a calculation has some uncertainty.

In the case of parameters I use as inputs (e.g. emitter temperature or collector voltage) I assume the number has machine precision regardless of the number of decimals I specify. Assuming there are 15 decimals worth of precision, if I specify the emitter barrier height as 

  1.4
  
I am effectively saying it is 

  1.40000000000000
  
This construction is a little silly, but it is what I am doing.

The second source of error are the values of the physical constants I use. I will use the most precise values I can find, but these values are always reported to a certain precision with a particular uncertainty. I believe this precision is less than the machine precision and therefore will be the predominant source of uncertainty in a value returned by a calculator method.

One final source of uncertainty, related to the uncertainty of physical constants, arises from the conversion of units. Converting cm^2 into m^2 doesn't change the relative uncertainty because there is an absolutely precise relationship between cm and m. In fact, Taylor mentions such a convention on p. 54. Converting other units like eV to J comes with an uncertainty because the conversion factor isn't absolutely precise -- in this case it is the value of the fundamental charge. The uncertainty of unit conversion of object data is noted in the docstring of the class.


Miscellany
==========
In an earlier draft of this style guide, I received several comments along the lines of, "It seems like you do an awful lot of API testing." I try to defensively write the API to avoid dumb numerical errors like unit conversion. For example, I've been using functionality from [`astropy.units`]() to handle units in my simulators. I can't say enough positive things about having numerical data types with units; this functionality eliminates a whole class of irritating bugs. There are whole sets of tests that test the output unit of the calculator methods I write.

Writing a comprehensive API test suite can become costly; subsequent changes to the API may require significant rewrites of the test suite.

Apparently people get into holy wars about test driven development. I read several things awhile back suggesting that unit testing can become ritualistic without value (or negative value if you have to refactor your test suite every time you make a small API change) and that you should concern yourself with writing integration tests instead. This advice rings true to me.

Here are some links possibly to that effect:

https://www.destroyallsoftware.com/blog/2014/test-isolation-is-about-avoiding-mocks
http://pythontesting.net/strategy/why-most-unit-testing-is-waste/
http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html


Skeleton test file
==================

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
For an excellent introduction to python testing, see Arbuckle (9781847198846).
