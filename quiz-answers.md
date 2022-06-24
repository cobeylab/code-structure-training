## Quiz Answers

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


