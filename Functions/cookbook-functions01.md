#### Python Cookbook: Functions | Step 1 of 9

# Writing Functions That Accept Any Number of Arguments

## Problem

---

You want to write a function that accepts any
number of input arguments.

## Solution

---

To write a function that accepts any number of positional
arguments, use a \* argument. For example:

<hr />

```

def avg(first, *rest):
    return (first + sum(rest)) / (1 + len(rest))

# Sample use

$ avg(1, 2)
$ avg(1, 2, 3, 4)

```

In this example, rest is a tuple of all the extra positional
arguments passed. The code treats it as a sequence
in performing subsequent calculations.

To accept any number of keyword arguments, use an argument
that starts with \*\*. For example:

```

import html
def make_element(name, value, **attrs):
    keyvals = [' %s="%s"' % item for item in attrs.items()]
    attr_str = ''.join(keyvals)
    element = '<{name}{attrs}>{value}</{name}>'.format(
                  name=name,
                  attrs=attr_str,
                  value=html.escape(value))
    return element

# Example
# Creates '<item size="large" quantity="6">Albatross</item>'

$ make_element('item', 'Albatross', size='large', quantity=6)

# Creates '<p>&lt;spam&gt;</p>'

$ make_element('p', '<spam>')


```

Here, attrs is a dictionary that holds the passed
keyword arguments (if any).

If you want a function that can accept both any number
of positional and keyword-only arguments,
use \* and \*\* together. For example:

```

def anyargs(*args, **kwargs):
    print(args)      # A tuple
    print(kwargs)    # A dict

```

With this function, all of the positional arguments are placed into a
tuple args, and all of the keyword arguments are placed into a
dictionary kwargs.

## Discussion

---

A _ argument can only appear as the last positional argument in a
function definition. A \*\* argument can only appear as the last argument.
A subtle aspect of function definitions is that arguments can still
appear after a _ argument.

```

def a(x, *args, y):
    pass

def b(x, *args, y, **kwargs):
    pass

```

Such arguments are known as keyword-only arguments.
