#### Python Cookbook: Classes & Objects | Step 1 of 6

# Changing the String Representation of Instances

## Problem

---

You want to change the output produced by printing or viewing
instances to something more sensible.

## Solution

---

To change the string representation of an instance, define
the **str**() and **repr**() methods.

<hr >

For example:

```

class Pair(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y
    def __repr__(self):
        return 'Pair({0.x!r}, {0.y!r})'.format(self)
    def __str__(self):
        return '({0.x!s}, {0.y!s})'.format(self)

```

The **repr**() method returns the code representation of
an instance, and is usually the text you would type to re-create
the instance. The built-in repr() function returns this text,
as does the interactive interpreter when inspecting values.
The **str**() method converts the instance to a string,
and is the output produced by the str() and print() functions.
For example:

```

$ p = Pair(3, 4)
$ p
$ print(p)

```

The implementation of this recipe also shows how different string
representations may be used during formatting. Specifically, the
special !r formatting code indicates that the output of **repr**()
should be used instead of **str**(), the default. You can try
this experiment with the preceding class to see this:

```

$ p = Pair(3, 4)
$ print('p is {0!r}'.format(p))
$ print('p is {0}'.format(p))

```

## Discussion

---

Defining **repr**() and **str**() is often good practice, as it can
simplify debugging and instance output. For example, by merely
printing or logging an instance, a programmer will be shown more
useful information about the instance contents.

It is standard practice for the output of **repr**() to produce
text such that eval(repr(x)) == x. If this is not possible or
desired, then it is common to create a useful textual representation
enclosed in < and > instead. For example:

```

> > > f = open('file.dat')
> > > f

<\_io.TextIOWrapper name='file.dat' mode='r' encoding='UTF-8'>

```

If no **str**() is defined, the output of **repr**()
is used as a fallback.

The use of format() in the solution might look a little funny, but the
format code {0.x} specifies the x-attribute of argument 0. So, in
the following function, the 0 is actually the instance self:

```

def **repr**(self):
return 'Pair({0.x!r}, {0.y!r})'.format(self)

```

As an alternative to this implementation, you could also use the
% operator and the following code:

```

def **repr**(self):
return 'Pair(%r, %r)' % (self.x, self.y)

```
