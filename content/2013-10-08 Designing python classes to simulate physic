Title: Designing python classes to simulate physical phenomena
Date: 2013-10-08 18:58
Category: Blog
Author: Joshua Ryan Smith
status: draft

Designing software to simulate physical phenomena is a nuanced art. Design decisions made early in the development process can have lasting negative consequences: the scientist software developer can back themself into a corner where making small changes or extension to the functionality require enormous efforts on the part of the developer. These decisions can result in the code evolving to be brittle and difficult to maintain. If the developer had knowledge of how the code should look from the outset, this wouldn't be much of a problem. Unfortunately, no one can predict the future, so software designers must rely on intuition gained from experience.

In this essay I will describe some design patterns I've found to be useful when writing code to simulate physical phenomena. These are simulations that do not require massive amounts of computational resources. In this essay, I assume the reader already follows good software development practices: they use version control, an issue tracker, they write documentation for their modules, classes, methods, etc. They write good tests.

I write code in python, so all the examples in this essay are in python. I also assume the reader is familiar with programming -- this essay is not a tutorial on python.

One of the foundational ideas is tto capture the problem in teh framework of object oriented programming. I've found that the fact that objects combine both data and functionality is an effective way to logically group the physical phenomena of interest (e.g. momentum, electron concentration, current density -- things that can be calculated using methods) together with the with the quantities on which those phenomena depend (mass, electron charge -- things that can be represented by class attributes). Many times, after the initial code has been written and hte original calculation has been performed, you discover that you want to perform a slightly different calculation. When code is written in the object-oriented framework, you simply add another method to the class definition. Any code that handles the data, loads, writes, etc. is already there. There are software developers out there whos skills are far superior to mine who are against OOP. Frankly, I don't really understand why. OOP seems to elegantly solve my problems and I haven't really seen a drawback.

I will write subclasses, but I don't typically use mixins or other multiple inheritance. If you need functionality within a class, its best to import the functionality from another module and leverage that functionality from within the class.

The power of dictionaries
-------------------------
Dictionaries may be the most useful data structure ever created. I design a lot of my classes and functinality around dictionaries. I've found that many novice programmers use some kind of iterable data type like a list or an array when they should be using a dictionary (I did when I was younger). Don't get me wrong, iteration is a powerful functionality. But many times, novice programmers use lists just for storage of data -- they don't even use the most powerful feature of these data types (which is iteration).

<DROP IN THAT BIG BLOCK OF COPY BELOW ABOUT WHY DICTS ARE THE BEST>


Stuff from the README
---------------------
Many data sets are two dimensional; typically these data sets are represented by two arrays where an item in one array corresponds to an item in the other. These items are sometimes called the x and y, the independent variable and dependent variable, the abscissa and ordinate, and so on. Dealing with this type of data (calculating it, plotting it) seems like it should be easy, but many times it is deceptively difficult.

In this essay I present a way of thinking about the problem of writing code to simulate a physical phenomenon. I describe some software design patterns common to people who simulate physical phenomena which I argue are bad software architecture. I then propose design patterns for these types of problems which I think are more general and better. Using the more general design patterns, I show how it is easy to create a library that can generate two dimensional data.

Here are some common problems:

* Dealing with metadata or fixed terms that aren't either the abscissa or ordinate, but parameterize the data set. Even if you record the data as two lists of equal length, these other values must be included somewhere.
* Precisely defining the abscissa and ordinate. For example, kinetic energy is defined as 

    E_kinetic = 0.5 * mass * velocity**2

Many times the abscissa will be velocity and the ordinate will be E_kinetic, but what if the velocity is fixed and the mass changes? Writing a library that takes velocity data as an array but mass data as a scalar doesn't generalize well.



The Problem
-----------
Many data sets are two dimensional, but this is a coincidence that can easily be mistaken for a fact. In fact, a dependent quantity of interest (e.g. energy, electron carrier concentration, etc.) typically depends on many different quantities. For example, the gravitational potential energy of two bodies is given by 

    U_gravity = (G * m_1 * m_2)/r

where G is the gravitational constant, m_1 is the mass of one of the bodies, m_2 is the mass of the other body, and r is the separation distance of the two bodies' center of mass. Most times, people would be interested in the value of the gravitational potential energy as a function of separation distance. When writing code to simulate this relationship, this idea of two-dimensional data is so powerful, that it is tempting to write the code codifying the two-dimensional relationship. In other words, it is tempting to write a method that returns an array for U_gravity for which the user passes scalar values for m_1 and m_2, but must pass an array for r. Writing the method this way is bad software architecting because it doesn't generalize. It assumes that the user will never want to calculate U_gravity as a function of either of the masses (or even the gravitational constant!). Changing the method to accept data of type vector for all of the quantities is also bad software architecting because now the returned quantity of U_gravity may be scalar, vector, or some multi-dimensional array. Handling all of these cases is a difficult problem and having to solve all of the related problems with generalizng the method in this way is distracting from the simulation problem at hand.

A better way to deal with this kind of method is to write it such that it accepts only scalar quantities for input, and it only returns a scalar quantity for output. This construction makes no assumptions about the relationships between the quantities and is therefore more general; the user can write code using iterators to encode whatever relationship he or she wants. Put another way, the user could start with an array for any of the input quantities and scalar values for the rest, then iterate over the array of inputs to generate an array of outputs. Furthermore, the user could have multiple arrays of input quantities and define the iteration however he or she desires to generate the output quantities.

Defining classes to address the problem
---------------------------------------
So now we've exposed an assumption that can lead to bad software architecture and we've touched on the general solution: when writing methods for physical relationships, the method should accept only scalar quantities and return a scalar quantity. This lesson generalizes to class definitions. When simulating physical phenomena, object-oriented programming can be helpful. You define a class to pull together some parameters you want, then you write methods to execute calculations on some subset of those parameters. If the methods are sufficiently simple, they can be easily combined in various ways to yield more complex calculations. The rule here would be to write methods that encode one and only one relationship.

Use dictionaries to collect parameters and instantiate objects
--------------------------------------------------------------
A data structure such as a python dictionary offers an excellent way to collect a set of parameters that make up the attributes of an object.

A python dictionary is an unordered collection of heterogeneous data whereas a python list or tuple are an ordered collections of homogeneous data. Additionally, a python dictionary provides the functionality to explicitly name the data contained within the dictionary.

Many novice programmers are tempted to use iterable types like lists or tuples to hold data in a situation where a dictionary is a better option. The easiest way to understand the benefits of using a dictionary is to list the shortcomings of a list. For the remainder of this argument I will refer to every type of iterable data structure as a list. I think that novice programmers are enticed into using lists for holding heterogeneous data because of the preeminance that languages like MATLAB give them. There's a lot of code out there where methods return lists of heterogeneous data and the user is expected to just know that the data they want is held at index 2. Moreover, these methods are written to accept a heterogeneous collection of data in list form -- again the user is just expected to know the proper ordering of the list items.

I argue that this pattern is bad software architecture. Explicit is better than implicit. Consider the example of the gravitational potential energy above: the way it is written, the value U_gravity depends on four different quantities. If we are going to write a method to return U_gravity that accepts a list as input, what's the proper ordering of input arguments? Don't tell me that it doesn't matter, because humans are pattern-matching machines; people *will* assign importance to each parameter depending on its position in the list even though the architect of the method may have chosen the order at random. There are 4! = 24 different ways to arrange the four parameters. In fact, for n parameters, there are n! permutations on the calling order.

Look, when we are dealing with heterogeneous data, there's no sense to impose a false order. It leads to misunderstandings.

Since the order of the arguments just doesn't matter, orderability is a superfluous feature for a data structure who's job it is to contain arguments. Worse than being superfluous, orderability can be a red herring that misleads the user.

On the other hand, explicit nameability of items is a useful feature for a data structure who's job it is to contain arguments. Dictionaries have exactly this feature. Moreover, dicts explicitly do not have any ordering so they prevent a bad pattern in software architecture.

Use verbs to describe the functionality of methods
--------------------------------------------------
I like to use the prefix "calc" for any method that calculates and returns a quantity. I also use the prefix "set".