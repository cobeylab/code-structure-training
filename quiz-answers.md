## Quiz Answers

### Part 1

1. All computation can be modeled as operations on ____.
  - ~~a. functions~~
  - b. **data**
  - ~~c. output~~
  - ~~d. input/output pairs~~

2. One core software organization principle is Separation of Concerns. "Concern" refers to:
  - ~~a. errors~~
  - ~~b. functions~~
  - c. **specialization or functionality**
  - ~~d. large files or functions~~

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
  - ~~True~~
  - **False**

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
  - **True**
  - ~~False~~

5. The area or areas in source code where a variable is defined and can be accessed is its:
  - a. **scope**
  - ~~b. definition~~
  - ~~c. function~~
  - ~~d. concern~~

### Part 2

1. Suppose you have a neighbor who fixes sweaters. There's a sign on their door that says, "Sweaters repaired here." You bring a torn sweater to their door and then pick up your repaired sweater later. If your neighbor is like an algorithm, what's the best analog for an _interface_?
  - a. **The door.**
  - ~~b. A sewing kit.~~
  - ~~c. The tear.~~
  - ~~d. Payment for their services.~~

2. Interfaces don't help the user reason about the code behind them. More formal interfaces are often called:
  - ~~a. applications~~
  - ~~b. validated~~
  - c. **well-defined**
  - ~~d. user interfaces~~

3. Two functions are functionally equivalent if they:
  - ~~a. Have the same variable names.~~
  - ~~b. Have the same interface.~~
  - c. **Have the same input/output pairs.**
  - ~~d. Use the same data.~~

4. A function ____ is its interface.
  - ~~a. body~~
  - b. **signature**
  - ~~c. definition~~
  - ~~d. table~~

5. Separation of Concerns can be achieved without knowing what the code does. Any set of 20 lines broken into four functions with five lines each is "separated" and "decoupled."
  - ~~True~~
  - **False**



