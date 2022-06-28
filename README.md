# Code Structure for Science
### How to write canonical software.

## Prerequisites

Some coding experience. Understanding of basic control flow (functions, conditions, loops). Examples are in Python but concepts apply to any language.

## Dependencies

* Python 3 (optional)


## What does "code structure" refer to?

This module covers the basic motivation and techniques for constructing software applications, scripts, and analyses out of the basic building blocks in programming languages. This goes by many names: organization, design, or architecture. And there are many code qualities we often want to optimize: readability, maintainability, cleanliness, or modularity.

We will focus on the basic aspects of structuring code at a small scale, and minimizing a unifying quality _complexity._ We will ignore other purposes for studying architecture such as performance, and specific design patterns.


## Motivation

Complexity is one of the main barriers to code reuse. It inhibits the author's ability to produce the code, and the user's ability to understand and sometimes to run the code at all. In science, this phenomenon is even more acute since the stakes may be the applications effect on the scientific community and scientific knowledge.

At the far end of the spectrum of unmaintainable code is code that, practically speaking, won't run without the author's file formats, local environment, or know-how. As an application gets larger, the effect is compounded. Large applications are much, much more complex than small ones. As an application grows, it's common to hit a "wall" where progress becomes difficult or prohibitive.

There's also a sense in which a scientific application or library serves as a description of a process, and should, as a mathematical formula, be put in simplest, most canonical form.

All of these factors affect progress, and digital reproducibility in science.


## Theoretical Framework

The foundations of code organization go back to the foundations of computation, and Computer Science. For the purpose of this module, we'll rely on three important facts:

1. All computation can be modeled as operations on data.
2. All computation can be modeled as functions.
3. There are nearly unlimited ways to write a piece code that are equivalent (meaning the functionality, or input/output pairs is the same).

A simpler, and correct, way to put this is that all computing, and therefore programming, consists of functions (composed of smaller computations) operating on data.

What does this mean, and why does it matter?

One source of complexity is the tendency of new programmers to think of the code they're writing in novel ways depending on the domain in which they're working, or what's convenient in a particular codebase. It may also be more difficult for a beginner programmer to deconstruct existing code, leading to great variety of patterns depending on what works at any given time.

The fact is that all code can be written in a very uniform way if you view it through a particular lens. Instead of variables with new names in each new project you take on, and functions that do things in novel ways, you can begin to see variables as data, and a series of functions that operate on that data.

Even operations that seem to need their own category, are actually the same. The `+` operator is just a function that takes two parameters. The expression `1 + 2` evaluates to 3, but so would a function `add` that does addition: `add(1, 2)`.

`1 < 2` evaluates to `True` but so would a function `less_than` that returns `True` if the first parameter is less than the second. Other structures are, and in fact have to be, rewriteable in this way.

We can also start to think about what we want from functions across all of our code to simplify anything we write, and make future changes easier as well (more on this later).


### Example

To take an extreme example, consider the following code.

```python
pos_a = 3

occ_a = 2

p_of_a = occ_a / pos_a

pos_b = 4

occ_b = 3

p_of_b = occ_b / pos_b

p_of_a_and_b = p_of_a * p_of_b

p_of_b_given_a = p_of_a_and_b / p_of_b

p_of_a_given_occ_b = p_of_a_and_b / p_of_b * occ_a / pos_a

p_of_a_given_b = p_of_a_given_occ_b / p_of_b

print(p_of_a_given_b)

# ~ 0.59
```

This code is perfectly legitimate in that it runs and does something useful, computing 0.59. There are three problems with its code structure, however.

1. It only computes one thing with one set of numbers as input. The values are "hard coded" and there are no functions to call again with different values.
2. There's repetition for what seems to be the same algorithm (`p_of_b` and `p_of_a` are computed the same way). There's repetition of the same calculation `p_of_a_and_b / p_of_b`.
3. There's very little abstraction. Each line does one operation and only the variable names give an idea of the author's intention.
4. There are different "levels" of abstraction. `p_of_a_given_occ_b` goes back to using input values when it could be composed of "higher level" computations.

There is no need to think about this code in a novel way. We don't even need to know what it does to make it simpler and more understandable. We know that it can be composed of functions and data.

First, recognize that variables such as `pos_b` are data, and they're unchanged throughout so we can group them together at the beginning. We can think of them as separate from computation.

We can also remove any repetition of the same calculation. (This doesn't go back to the Theoretical Framework but it adds complexity and is very common. More on this in [DRY](#dont-repeat-yourself-dry). The issue of using different levels of abstraction is also fixed this way and can be thought of as an application of the DRY principle.)

```python
pos_a = 3
pos_b = 4

occ_a = 2
occ_b = 3

p_of_a = occ_a / pos_a

p_of_b = occ_b / pos_b

p_of_a_and_b = p_of_a * p_of_b

p_of_b_given_a = p_of_a_and_b / p_of_b

p_of_a_given_b_occ = p_of_b_given_a * p_of_a

p_of_a_given_b = p_of_a_given_b_occ / p_of_b

print(p_of_a_given_b)

# ~ 0.59
```


It's a little easier to understand already. Now let's generalize over some of the instances of division with a function `p`.

And now that we know that `p_of_a` and `p_of_b` are the result of the `p` computation (which is really looking like probability) we can change them to `A` and `B`.

```python
pos_a = 3
pos_b = 4

occ_a = 2
occ_b = 3

def p(num, denom):
    return num / denom

A = p(occ_a, pos_a)
B = p(occ_b, pos_b)

p_of_a_and_b = A * B

p_of_b_given_a = p_of_a_and_b / B

p_of_a_given_occ_b = p_of_b_given_a * A

p_of_a_given_b = p_of_a_given_occ_b / B

print(p_of_a_given_b)

```

So now it's clear that the intention of those division operations are meant to be probabilities. (Usually, we'll want to abstract more code at once since `p(x, y)` is not much simpler than `x / y`, if at all, but it illustrates the concept.)

Now we can abstract more code into a function. This will make these lines reusable: We've generalized over all computations of this type.

By using some logical replacement, replacing each variable in subsequent lines with its definition, we can start to combine the expressions.

```python
p_of_a_and_b = A * B

p_of_b_given_a = p_of_a_and_b / B

```

Becomes:

```python

p_of_b_given_a = A * B / B

```

Continuing that process leaves a very simple equation:

```python
pos_a = 3
pos_b = 4

occ_a = 2
occ_b = 3

def p(num, denom):
    return num / denom

A = p(occ_a, pos_a)
B = p(occ_b, pos_b)

p_of_a_given_b = A * B / B * A / B

print(p_of_a_given_b)

```

And finally, we make this a reusable function:


```python
def p(num, denom):
    return num / denom

def bayes_rule(A, B):
    return A * B / B * A / B

pos_a = 3
pos_b = 4

occ_a = 2
occ_b = 3

A = p(occ_a, pos_a)
B = p(occ_b, pos_b)

A_given_B = bayes_rule(A, B)

print(A_given_B)
```

For a more complex operation, supporting functions like `p` are helpful. Very complex processes can be written as trees of functions breaking work down into smaller and smaller pieces. For such a short function, and since the domain now is clear, `p` isn't necessary:

```python
def bayes_rule(A, B):
   return A * B / B * A / B

A = 2 / 3
B = 3 / 4

A_given_B = bayes_rule(A, B)

print(A_given_B)

```


Now consider this program:

```python
def calc(xy, ij):

    result = [None, None]

    for idx in [0, 1]:
        result[idx] = xy[idx] / ij[idx]

    first = 0
    second = 1
    return result[first] * result[second] / result[second] * result[first] / result[second]

array1 = [2, 3]
array2 = [3, 4]

print(calc(array1, array2))
```

It is identical to the previous program.

Again, we can use our lens to see the functions as a set of input/output pairs; operations on data. The exact code is not the "definition" of the program. The definition is the set of input/output pairs.

To convince ourselves further, let's do an experiment and generate random inputs and see if the functions' output is identical:


```python
from random import randint

def calc(xy, ij):

    result = [None, None]

    for idx in [0, 1]:
        result[idx] = xy[idx] / ij[idx]

    first = 0
    second = 1
    return result[first] * result[second] / result[second] * result[first] / result[second]

def bayes_rule(A, B):
   return A * B / B * A / B

for x in range(0, 1000):
    min = 1 # avoid div by zero
    max = 100

    pos_a = randint(min, max)
    occ_a = randint(min, max)

    pos_b = randint(min, max)
    occ_b = randint(min, max)

    assert bayes_rule(occ_a/pos_a, occ_b/pos_b) == calc([occ_a, occ_b], [pos_a, pos_b])
    print("Same!")
```

And they are. These two code snippets are examples of an endless number of ways the same functionality can be written.

Hopefully, this shows intuitively how guarantees from computational theory apply to everyday programming, and how code can be organized consistently throughout a program, and over programs in general.

Next we'll cover the range of individual functions and how they can be easier, or more difficult to reason about and maintain.


## Function Design

We know programs can be decomposed into functions and data. We know that by using this lens, we can organize programs around this dichotomy, and we're guaranteed to be able to do so. Now we can talk about how to optimize each function for minimum complexity.


### Scope

Most programming languages include the concept of scope. Scope is the section of source code in which a variable or function can be accessed. It is the place or places where a name is defined.

```python
def somefunc():
    a = 1
    return a + 1

print(somefunc())

# a will not be defined
a
```

If you run this code, you should get an error "name 'a' is not defined." This is because the variable `a` is defined in the function `somefunc` and undefined outside the scope of the function, meaning anywhere outside the body of the function.

This is very important for the ability to reason about programs. If `a` is not defined outside of the function, we don't need to think about it anywhere but inside the function. We can assume that, if our code compiles, there's no reference to `a` anywhere else, and there's no code that depends on `a`, or modifies it.

Conclusions about large programs often hinge on this logic.

Take the "replacement rule," used to simplify the Bayes program.

```python
p_of_a_and_b = A * B

p_of_b_given_a = p_of_a_and_b / B
```

is equivalent to:

```python
p_of_b_given_a = A * B / B
```

But what if `p_of_a_and_b` could be modified by a parallel process after it's initialized to `A * B` and before it's accessed in the next line?

What if we could modify a variable anywhere?

### Fig. A
```python
x = 3
y = 2
run_something()

print(x * y)
```

Lest we forget how malleable (and how large and complex) software can be, `run_something()` could be thousands of lines of code, and we would not be guaranteed of any `x * y` because `run_something` may have changed them. We certainly couldn't use the logic of replacement to replace `x` with `3` in the last line and be certain the output is unchanged.

_Note: In Python, `run_something` could modify `x` or `y` but they would need to be prefaced with `global` in the function._

The analogy in scientific publishing would be that a variable x in a complex scientific paper could be affected by other functions, not because they are involved in the computation of x, but because the functions were "nearby" x, or in the same paper as x. It's much more complex to understand some equations when everything depends on everything else, rather than when we're able to think about equations in isolation.

So scope is very important. It guarantees that we can isolate variables and make logical conclusions about them.


## The Range of Functions

Despite the fact that scopes prevent us from modifying variables outside their intended context, each scope can still be very complex. There is an unlimited number of variables in and outside of functions, and, with some small tweaks, the `x` and `y` above could be changed by some code in `run_something`.

Effort is required to keep individual scopes simple, and avoid changes or dependency on variables in other parts of a program. One of the easiest ways to do this, and a way that's guaranteed possible by the [theoretical framework](#theoretical-framework), is by favoring pure functions.


### Pure Functions

Pure functions are functions that have an input and an output (parameters, and return value). This is no different from other functions. However, the output of a pure function _only depends_ on the input. And, the function _only affects_ the output.

The way this is stated more formally is:

1. Pure functions always return the same output for an input. For instance:

```python
def f(x):
    return x + 2
```

always returns the corresponding `x + 2` for every `x` no matter the context and across time.


```python
from random import randint

def f(x):
    return x + randint(0, 10)
```

does not.

2. Pure functions have no side-effects. The function can’t modify any variables outside itself.

```python
g = 0

def f(x):
    global g
    g = g + 1
    return x + 2
```

`f` modifies y, which is outside the scope of the function, so it is not pure.


Another way to describe pure functions, and their benefits, is to say that pure functions are [_referentially transparent_](https://en.wikipedia.org/wiki/Referential_transparency). You can replace the symbol (`p_of_a_and_b` in the previous example, or `f(2)`) with the code that computes it anywhere it appears, and the behavior of the program will remain the same. For complex programs, this is invaluable. Programs often need to be rewritten, as mathematical equations are, to simplify them, to learn something new from them, or reuse them in novel ways.

Even if a program doesn't need to be rewritten, pure functions tend to be simpler to understand. They can be reasoned about in isolation, and without knowing the entire history of a particular run of the program. Remember, in the [example above](#fig-a), it's not just that `run_something` _can_ modify `x` or `y`, it's that you also need to figure out if `run_something` _will have been called_ in a particular run of the program.


### Limits to Functional Programming

Functional Programming, or programming with pure functions, is part of a foundational understanding of modularity, testability, and complexity. Taken to the extreme, functional programming renders a program, in the words of one of the authors of the Haskell programming language, Simon Peyton Jones, "useless." A strictly pure application cannot have any side effects, and cannot effect the world. Running it on a computer will have one effect. The machine ["gets hot."](https://youtu.be/iSmkqocn0oQ?t=193)

This is a slight exaggeration since functional programs are usually allowed to have the side effect of writing their output to disk, or the terminal, but it illustrates that there could be some limit to how far to take this. The metric that we're after here is simplicity. If isolating computation into pure functions makes it simpler, it makes sense that breaking the challenge of avoiding side effects into small pieces does too. In other words, if it is more difficult to think about writing a large application that behaves as a pure function, simplicity is lost. Break the problem into pure functions whenever possible, and simplify dependencies and side effects elsewhere.

A great example of system design that has lasted for over 50 years is Unix, which has a lot in common with limited functional programming. 1. Model computation as sets of input and output. 2. Write small pure functions (commands). 3. Use side effects liberally at the boundaries of these functions (usually writing to disk or the terminal).

Purely functional programs also have limitations in that they tend to have longer and longer function signatures because parameters need to be passed around instead of being made global. This tendency can be reduced by allowing immutable global variables, and in some languages such as Scala, _implicit parameters_.

Certain categories of functionality that are more difficult in functional programming such as writing to a log, can simply be allowed, given that they don't normally affect return values and are easy to spot while reading.

For these reasons, functional programming is generally considered a North Star and not a strict goal to be achieved. For most programming tasks, the direction towards simplicity is _in the direction of_ pure functions.


## Separation of Concerns

Another principle of code structure is Separation of Concerns. Separating concerns is breaking a program into discrete components not unlike functional programming. Separation of Concerns goes further to say that the functionality of each component should be one "concern," meaning it should be specialized. This usually happens naturally no matter why you're  making your code more modular but the principle that concerns should be separated highlights the fact that modularity is useless as a principle for thinking about things in isolation if the modules aren't _functionally_ isolated. Their functionality overlaps in complex ways even if their variables do not.

Separation of Concerns is also limited to being a subjective measurement by a very human definition of what a "concern" is. Concerns can be thought of analytically as _things that can be changed in isolation_ without changing functionality of the application. This will depend on the application as a whole, but it gives an objective measure given some real-world concern.

For example:

```python
def multiply(x, y):
    print(f"The answer to {x} * {y} is: {x * y}")

def divide(x, y):
    print(f"The answer to {x} / {y} is: {x / y}")

multiply(1, 2)
divide(2, 3)
```

Not only does this program not allow you to write

```python
divide(multiply(1, 2), 3)
```

because each function has taken upon itself the concern of printing out the answer, it also duplicates functionality. If you wanted to change the text of the output to "The expression evaluates to," it would involve changing the text in several places.

Separating Concerns could look like:

### Fig. B

```python
def multiply(x, y):
    e = x * y
    report(x, y, "*", e)
    return e

def divide(x, y):
    e = x / y
    report(x, y, "/", e)
    return e

def report(a, b, op, out):
  print(f"The answer to {a} {op} {b} is: {out}")

divide(multiply(1, 2), 3)
```

Or, to keep side effects to the top level (to make it purely functional):

### Fig. C

```python
def multiply(x, y):
    return x * y

def divide(x, y):
    return x / y

def report(a, b, op, out):
    return f"The answer to {a} {op} {b} is: {out}"

# inputs
x, y, z = 1, 2, 3

# compute
mult = multiply(x, y)
div = divide(mult, z)

# report
print(report(x, y, "*", mult))
print(report(mult, z, "/", div))
```

Notice, despite the extra overhead of saving the intermediate values `mult` and `div` so they can be reported, this version neatly segments the program into inputs, computation, output, and interaction with the user (printing the `report` output).

This may seem like a silly example since the reorganization was so simple and obvious but this mimics large and prevalent issues. Think of pulling a numerical value out of the string "The answer to..." as pulling data out of a PDF or Word Document, or web scraping. And nearly every large program, and some small ones, have a version of not being able to simply write `divide(multiply(1, 2), 3)` because the output is not returned from the inner function, or there are unwanted side effects or dependencies.

The connection between Separation of Concerns and the foundational principles of computing, is that the principles guarantee that _any separation of concerns_ is possible. In the most extreme code refactoring, every computation is broken into the smallest units possible, and the desired separation of concerns is simply a repartition of those units (the only limitation being values that depend on other values).

## Don't Repeat Yourself (DRY)

The simplest way to add complexity to code is to write the same functionality in more than one place. This is an obvious but important principle with some subtlety. Once writing functions is trivial, moving anything that's written more than once into a function and calling the function is too. Particularly if the other principles such as avoiding dependencies and side-effects (purity), and SoC are followed, it's hard to go wrong with this process.

DRY also applies to variables and other data. Two values that represent the same thing is usually unnecessary complexity.

The subtlety of DRY coding is that repetition can have some benefits. One is the ability to modify copies of some value or algorithm independently. It can be confusing to "centralize" a piece of code and then have some variations elsewhere. The easy answer then is to add parameters so the variations are covered by the centralized copy but this is not always possible to do cleanly. And often code that is duplicated is short enough to be on the bubble between abstract-able and not. If you centralize down to a microscopic level, you end up with a new programming language all your own, and you've only added complexity.

Repetition is also used to make a small process more clear in place, to prevent single-point-of-failure (say two teams working independently on the same problem), and to avoid locking a codebase into a certain architecture too early. These are all legitimate uses of repetition. As a general rule, though, repetition should be avoided. Coupled with the other principles covered, DRY will tend to make code simpler and more canonical.


## Unifying Example: Model View Controller

As a unifying example, there is a methodology called Model View Controller (MVC) that says an application should be broken into functionality (the model, `multiply` and `divide` functions in [Fig. C above](#fig-c)), control of that functionality (basically, the function calls at the bottom), and the user interface (`report` function and calls thereof). This is a partitioning that can be used to write almost any computer program, and MVC has been around for 40+ years. The reason programs can be written, or rewritten, this way is due to the [theoretical framework](#theoretical-framework) and the foundations of computation.

_Note: The [intermediate form above](#fig-b) (Fig. B) would not qualify as MVC since the concern of printing (which is part of the View, or interface with the user) is mixed in with the Model._

## Quiz

1. All computation can be modeled as operations on ____.
  - a. functions
  - b. data
  - c. output
  - d. input/output pairs

2. One core software organization principle is Separation of Concerns. "Concern" refers to:
  - a. errors
  - b. functions
  - c. specialization or functionality
  - d. large files or functions

3. In the following example, `remove_last` is a pure function. True or false?

```python
global errors
errors = 0

def remove_last(a):
    global errors
    s = a.split(' ')
    if len(s) == 0:
        errors += 1
        return []

    return s[:-1]

remove_last("one two")
remove_last("one")
remove_last("one ")
```
  - True
  - False

4. In the following example, `remove_last` is a pure function. True or false?

```python
def remove_last(a, errors):
    s = a.split(' ')
    if len(s) <= 1:
        return ([], errors + 1)

    return (s[:-1], errors)

errors = 0
(result, errors) = remove_last("one two", errors)
(result, errors) = remove_last("one", errors)
(result, errors) = remove_last("one ", errors)
```
  - True
  - False

5. The area or areas in source code where a variable is defined and can be accessed is its:
  - a. scope
  - b. definition
  - c. function
  - d. concern

### [Quiz Answers](quiz-answers.md)

## Funding and Acknowledgments

This work was funded by [Centers of Excellence for Influenza Research and Response (CEIRR)](https://www.ceirr-network.org/).

Portions of this tutorial were adapted from the [Cobey Lab Handbook](https://cobeylab.github.io/lab_handbook/).


