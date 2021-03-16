#### Python Cookbook: Classes & Objects | Step 3 of 6

# Simplifying the Initialization of Data Structures

## Problem

---

You are writing a lot of classes that serve as data
structures, but you are getting tired of writing
highly repetitive and boilerplate **init**() functions.

## Solution

---

You can often generalize the initialization of data
structures into a single **init**() function
defined in a common base class.

<hr >

For example:

```
class Structure(object): # Class variable that specifies expected fields
    _fields= []
    def **init**(self, *args):
    if len(args) != len(self._fields):
        raise TypeError('Expected {} arguments'.format(len(self._fields)))
    # Set the arguments
    for name, value in zip(self._fields, args):
    setattr(self, name, value)

# Example class definitions

if **name** == '**main**':
    class Stock(Structure):
        _fields = ['name', 'shares', 'price']
    class Point(Structure):
        _fields = ['x','y']
    class Circle(Structure):
        _fields = ['radius']
    def area(self):
    return math.pi * self.radius ** 2

```

If you use the resulting classes, you’ll find that
they are easy to construct.

For example:

```
$ s = Stock('ACME', 50, 91.1)
$ p = Point(2, 3)
$ c = Circle(4.5)
$ s2 = Stock('ACME', 50) # Results in an error

```

Should you decide to support keyword arguments,
there are several design options. One choice is
to map the keyword arguments so that they only
correspond to the attribute names specified
in "\_fields". For example:

```

class Structure(object):
    _fields= []
    def **init**(self, *args, **kwargs):
    if len(args) > len(self._fields):
        raise TypeError('Expected {} arguments'.format(len(self._fields)))
    # Set all of the positional arguments
    for name, value in zip(self._fields, args):
        setattr(self, name, value)
    # Set the remaining keyword arguments
    for name in self._fields[len(args):]:
        setattr(self, name, kwargs.pop(name))
    # Check for any remaining unknown arguments
    if kwargs:
        raise TypeError('Invalid argument(s): {}'.format(','.join(kwargs)))

# Example use

if **name** == '**main**':
class Stock(Structure):
_fields = ['name', 'shares', 'price']

s1 = Stock('ACME', 50, 91.1)
s2 = Stock('ACME', 50, price=91.1)
s3 = Stock('ACME', shares=50, price=91.1)


```

Another possible choice is to use keyword arguments
as a means for adding additional attributes to
the structure not specified in \_fields.

For example:

```

class Structure(object):
    # Class variable that specifies expected fields
    _fields= []
    def **init**(self, *args, **kwargs):
    if len(args) != len(self._fields):
        raise TypeError('Expected {} arguments'.format(len(self._fields)))
    # Set the arguments
    for name, value in zip(self._fields, args):
        setattr(self, name, value)
    # Set the additional arguments (if any)
    extra_args = kwargs.keys() - self._fields
    for name in extra_args:
        setattr(self, name, kwargs.pop(name))
    if kwargs:
        raise TypeError('Duplicate values for {}'.format(','.join(kwargs)))

# Example use

if **name** == '**main**':
class Stock(Structure):
    _fields = ['name', 'shares', 'price']

s1 = Stock('ACME', 50, 91.1)
s2 = Stock('ACME', 50, 91.1, date='8/2/2012')

```

## Discussion

---

This technique of defining a general purpose **init**() method can
be extremely useful if you’re ever writing a program built around
a large number of small data structures. It leads to much less
code than manually writing **init**() methods like this:

```

class Stock(object):
    def **init**(self, name, shares, price):
    self.name = name
    self.shares = shares
    self.price = price

class Point(object):
    def **init**(self, x, y):
    self.x = x
    self.y = y

class Circle(object):
    def **init**(self, radius):
    self.radius = radius
    def area(self):
    return math.pi * self.radius ** 2

```

One subtle aspect of the implementation concerns the mechanism
used to set value using the setattr() function. Instead of
doing that, you might be inclined to directly access the
instance dictionary. For example:

```

class Structure(object):
    # Class variable that specifies expected fields
    _fields= []
    def **init**(self, *args):
    if len(args) != len(self._fields):
    raise TypeError('Expected {} arguments'.format(len(self._fields)))
    # Set the arguments (alternate)
    self.**dict**.update(zip(self._fields,args))

```

Although this works, it’s often not safe to make
assumptions about the implementation of a subclass.
If a subclass decided to use **slots** or wrap a
specific attribute with a property (or descriptor),
directly acccessing the instance dictionary would break.
The solution has been written to be as general purpose
as possible and not to make any assumptions about subclasses.

A potential downside of this technique is that it impacts
documentation and help features of IDEs. If a user asks
for help on a specific class, the required arguments aren’t
described in the usual way.

For example:

```

help(Stock)

```

Many of these problems can be fixed by either attaching or
enforcing a type signature in the **init**() function.

It should be noted that it is also possible to automatically
initialize instance variables using a utility function and a
so-called "frame hack."

For example:

```

def init_fromlocals(self):
    import sys
    locs = sys._getframe(1).f_locals
    for k, v in locs.items():
        if k != 'self':
            setattr(self, k, v)

class Stock(object):
    def **init**(self, name, shares, price):
    init_fromlocals(self)

```

In this variation, the init_fromlocals() function uses
sys.\_getframe() to peek at the local variables of the
calling method. If used as the first step of an **init**()
method, the local variables will be the same as the
passed arguments and can be easily used to set attributes
with the same names. Although this approach avoids the
problem of getting the right calling signature in IDEs,
it runs more than 50% slower than the solution provided
in the recipe, requires more typing, and involves more
sophisticated magic behind the scenes. If your code doesn’t
need this extra power, often times the simpler solution
will work just fine.

```

```
