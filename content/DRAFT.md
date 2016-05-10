Title: Testing numerical software in python
Date: 2016-04-02
Category: Blog
Author: Joshua Ryan Smith
Summary: A strategy to test numerical software written in Python.


# Introduction
When writing computational software, it is very important that numerical calculator functions return the correct results.
Ideally, you want a suite of tests that check the accuracy of these kinds of functions so that you can be sure changes to the code do not change the results of your calculator methods.

It turns out that the overall strategy for numerical testing is quite simple: evaluate the result of a calculator method against a known standard value and ensure the two values match to within an acceptable uncertainty.
Despite this simplicity, I've still gotten tripped up on some of the implementation details of these kinds of tests.
My goal with this essay is to carefully describe all of the components of numerical testing, and develop a framework in which it is easy to think about and write effective numerical tests.

This essay has the following structure: First I will clearly define the components of numerical tests and how they relate to one another. In this way it will be easier to categorize the test code you write and the data used in testing.





# The key concept: testing output against standard data
To be very pedantic about this issue: the goal is to test the relation of three things:

1. standard abscissae (independent variables)
2. an operation
3. standard ordinates (dependent variables)

The combination of "standard abscissae" and corresponding "standard ordinates" will be referred to as "standard data".
The "operation" is the relationship between the standard abscissae and corresponding standard ordinates, in the most general of terms.
The operation occurs on some abscissae and results in some ordinates.
For example, an operation could be addition of two numbers or the determinant of a matrix, etc.
A "function" is the particular implementation of the operation, and the "function under test" is the specific function to test.
"Function ordinates" are the outputs of the function.

The general form of the test is very simple: 

1. Acquire a set of standard data such that the standard abscissae and standard ordinates are verifiably related by the operation that is to be implemented by the function under test.
2. Pass the standard abscissae to the function under test.
3. Compare the output of the function under test to the standard ordinates. The test passes if the standard ordinates and function ordinates sufficiently match.

Most of the work in this workflow is defining and generating the appropriate standard data.


# Properties of standard data
You should aim to define and generate standard data with the following properties:

* Easily verifiable
* General
* Reasonably comprehensive
* Reasonably covers edge cases


## Easily verifiable
Verifying the correspondance between standard ordinates and standard abscissae via an operation should be as straightforward as possible.
For example, consider a function that implements the pythagorean theorem.
The pythagorean triple 3, 4, 5 is well known and easily verifiable with pen and paper.
Therefore, the 3, 4, 5 triple is a good test case for the function.

Note that although the triple 3, 4, 5 is sometimes referred to as a "special case" in the mathematical context, I am making a distinction between "special case" and "edge case" as defined below.


## General
It is best if standard data can be used to test more than one function in a program.
Sometimes test functionality can be achieved by combining sets of standard data.
Additionally, the proper test functionality can sometimes be achieved by slightly modifying the standard data -- these cases should be documented.


## Reasonably comprehensive
Enough cases should be tested to reasonably cover the input parameter space.
Despite the fact that floating point mathematics is actually discrete; it is unreasonable in many cases to attempt to test every combination of input parameters, so just try to make it across the parameter space.


## Reasonably covers edge cases
[Wikipedia says](https://en.wikipedia.org/wiki/Edge_case):

> An edge case is a problem or situation that occurs only at an extreme
> (maximum or minimum) operating parameter.

Like comprehensiveness, it may be possible to go overboard with testing edge cases.

Note that edge cases are not the same as "special cases" as defined above, nor are they the same as "exceptional cases" -- those cases which are known to raise an exception.


# Approaches to generating standard data
Standard data can be specified and generated at several different levels:

1. Write standard data directly into the test.
2. Write a helper function that generates the standard data for the test at test runtime.
3. Write a helper function that generates the standard data for the test and saves it to disk.


## Standard data within test
The advantages of this approach are simplicity.
Combining the standard data with the test means everything is in the same location, and it is therefore easier to reason about what the test is doing.
In addition, presumably the standard data is simple enough so that verification is trivial to easy.

The disadvantages of this approach is loss of generality.
standard data written into a test cannot be used by another test.

The balance here, like with most things in programming, is between simplicity and generality.


## Digression: purpose and scope of standard data generation helper functions
Before discussing the pros and cons of programatically generating standard data at test runtime or before, it is worth understanding the purpose and scope of standard data generation helper functions.
To achieve generality, standard data must be separated from particular tests.
In some cases, standard data can be written by hand, but there are many situations in which standard data is best generated with a helper function.
For example, perhaps a test requires a list with 10,000 items.

Writing a function to test another function is reminiscent of the joke,

> Some people, when confronted with a problem, think
> “I know, I'll use regular expressions.”   Now they have two problems.

When writing standard data generation helper functions the above principles of verifiability and reasonable comprehensiveness should be followed.
Practically speaking, there are only a few approaches to implementing such helper functions:

1. Leverage well-tested, mature code or libraries
2. Write the simplest helper function possible
3. Use the function under test to generate verifiable data

If a mature library exists to calculate the necessary standard data, you can feel more assured that the results are correct.
In all cases though, the data generated by any of these methods should be easily verifiable.
Additionally, there should be enough data to cover the parameter space, but not too much to prevent verification.

In all cases, the generation of standard data via helper functions should be well understood, well documented, and managed well (i.e. using version control) to ensure repeatability.


## Helper functions generating standard data at runtime
The advantage of this approach is more generality than writing standard data into a test: other tests can leverage the same data.
Another aspect of this approch is that generated data does not need to be managed; it can be generated on demand.
This aspect has the possible advantage (depending on your perspective) of not having to manage the data generated by this kind of test (e.g. by commiting to the repo).
One disadvantage is that executing these tests can add to computational overhead of running tests.
Another disadvantage is that managing the data generated is more implicit: commiting generated data to a repository means that the code and environment used to generate the data can, in principle, be specified.


## Helper functions generating standard data that is saved
Again, with this approach greater generality of standard data is achieved.
Depending on your perspective, saving the data generated in this way could be an advantage.
For one, regression testing can be performed on subsequent sets of generated data.
Second, the computational cost of generating the data is only paid once if the data is saved.

Saving the data in this way could also be a disadvantage: the data could be large and unwieldy to include in the code repository.
Additionally, saving the data in this way creates an additional data management task whos evolution is weakly coupled to the evolution of the codebase.


# Issues with saving programatically generated standard data
I think I lean towards programatically generating standard data and saving it.
I particularly like the advantage of regression testing the standard data generators, and the advantage of regression testing the functionality under test against multiple versions of generated data.
I also like the advantage of reducing the computational overhead of tests.

Managing saved data comes with its own set of issues.
Should the data be committed to the code repository or version controlled separately?
Should the data generating functions be stored in the same file as the unit tests?
How should I document the generation and saving of standard data?
How should I store the data (i.e. in the directory tree? python pickles? a gzipped tar file?)?


## 80% solution
For now, I think I'm going to store data in a subdirectory `tests/data`.
I am going to write standard data generators in the same file as the tests.
Eventually, I think I will start seeing patterns emerge in how I'm using all of these components and I will move things around based on a better understanding of how the parts fit together.






Assumptions
-----------
This entire testing strategy is based on the assumption that the computer can accurately and repeatably perform simple calculations. I assume that as far as the computer is concerned, the product

    2 * 5
  
is just as easy to calculate as the product

    2.333690544228055 * 5.44073192976832565

and the result is just as accurate. Most calculator methods are straightforward arithmetical operations, and the important tasks are to analyze the uncertainty propagation and verify methods' return values do not change over time (regression testing).

In the case of parameters used as inputs to methods (e.g. parameters like temperature or voltage) I assume the number has machine precision regardless of the number of decimals specified. For example, assuming there are 15 decimals worth of precision, if I specify a Schottky barrier as

  1.4
  
I am effectively assuming it is 

  1.40000000000000
  

Uncertainty analysis, uncertainty quantification, and units
-----------------------------------------------------------
Most calculator methods are straightforward mathematical operations like multiplication, division, addition, subtraction, and things like `exp()`. These calculator methods can be thought of as very simple algorithms; they are just a one-line calculation. I can very easily evaluate the uncertainty propagation through these calculators and determine what it means to pass these tests.

In the calculator methods there are three sources of uncertainty. 

One, the machine uses finite precision arithmetic.

The second source of error are the values of the physical constants. I will use the most precise values I can find, but these values are always reported to a certain precision with a particular uncertainty. Physical constants' precision is less than the machine precision and therefore will be the predominant source of uncertainty in a value returned by a calculator method.

One final source of uncertainty, related to the uncertainty of physical constants, arises from the conversion of units. Converting cm^2 into m^2 doesn't change the relative uncertainty because there is an absolutely precise relationship between cm and m (cf. [Taylor](https://openlibrary.org/books/OL8411964M/An_Introduction_to_Error_Analysis) p. 54). Converting other units like eV to J comes with an uncertainty because the conversion factor isn't absolutely precise -- in this case it is the value of the fundamental charge. The uncertainty of unit conversion of object data is noted in the docstring of the class.


Reasonableness of test coverage
-------------------------------
For every calculator method there is a tremendous number of combinations of input paramters. The problem isn't that it would take an unacceptably long time with unacceptably large computing resources to exhaustively calculate all of those combinations. The problem is that there's no reasonable way to check the resulting set of data.

Since checking a comprehensive standard set of data is unreasonable, the next approach would be to have a much smaller set of standard data which is a subset of the comprehensive set of standard data. The question now becomes: how small does the subset need to be? If the subset gets too big, the problem of reasonably checking all the values re-emerges.

Since we've already assumed that the computer accurately and repeatably calculates arithmatic operations, the subset of standard data can be quite small. Given that the human element is the limiting factor in terms of ease of performing a numerical calculation,  it is best to choose special case parameters so that humans can quickly spot-check all of the standard data.

The foundation of this testing strategy is to check the uncertainty propagation of each algorithm to get a picture of the uncertainty of compound algorithms. The strategy of checking algorithms is much better than slavishly checking sets of standard data.


Testing strategy
----------------
The typical strategy to test a calculator method has the following components. First, the uncertainty of the algorithm implemented by the calculator method is analyzed using uncertainty propagation theory. This analysis will appear in the docstring of the unit test which performs the numerical of that calculator method. Code will be written that generates easily checked-by-humans data. This code is used to generate the standard set of data for the calculator method. The standard set of data is generated over a range of input values which are reasonable (i.e. temperature will never approach 1e9K in a semiconductor simulation). The numerical accuracy unit test for the calculator method under test will compare the output of the calculator method to the (verified) output of the standard data. Each calculator method will usually only have one numerical accuracy unit test.