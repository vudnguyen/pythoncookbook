#### Python Cookbook: Functions | Step 9 of 9

# Accessing Variables Defined Inside a Closure

## Problem

---

You would like to extend a closure with functions that allow the
inner variables to be accessed and modified.

## Solution

---

Normally, the inner variables of a closure are completely hidden
to the outside world. However, you can provide access by writing
accessor functions and attaching them to the closure as
function attributes.

<hr>

For example:

```

def sample():
    n = 0
    # Closure function
    def func():
        print('n=', n)
    # Accessor methods for n
    def get_n():
        return n
    def set_n(value):
        nonlocal n
        n = value
    # Attach as function attributes
    func.get_n = get_n
    func.set_n = set_n
    return func

```

Here is an example of using this code:

$ f = sample()
$ f()
$ f.set_n(10)
$ f()
\$ f.get_n()

## Discussion

There are two main features that make this recipe work.
First, nonlocal declarations make it possible to write
functions that change inner variables. Second, function
attributes allow the accessor methods to be attached
to the closure function in a straightforward manner
where they work a lot like instance methods (even
though no class is involved).

A slight extension to this recipe can be made to have
closures emulate instances of a class. All you need to
do is copy the inner functions over to the dictionary
of an instance and return it. For example:

```

import sys
class ClosureInstance(object):
    def __init__(self, locals=None):
        if locals is None:
            locals = sys._getframe(1).f_locals
        # Update instance dictionary with callables
        self.__dict__.update((key,value) for key, value in locals.items()
                             if callable(value) )
    # Redirect special methods
    def __len__(self):
        return self.__dict__['__len__']()

```

# Example use

```

def Stack():
    items = []
    def push(item):
        items.append(item)
    def pop():
        return items.pop()
    def __len__():
        return len(items)
    return ClosureInstance()

```

Here’s an interactive session to show that it actually works:

$ s = Stack()
$ s
$ s.push(10)
$ s.push(20)
$ s.push('Hello')
$ len(s)
$ s.pop()
$ s.pop()
\$ s.pop()

Interestingly, this code runs a bit faster than using a normal
class definition. For example, you might be inclined to test
the performance against a class like this:

```

class Stack2(object):
    def __init__(self):
        self.items = []
    def push(self, item):
        self.items.append(item)
    def pop(self):
        return self.items.pop()
    def __len__(self):
        return len(self.items)

```

If you do, you’ll get results similar to the following:

```

from timeit import timeit
# Test involving closures
s = Stack()
timeit('s.push(1);s.pop()', 'from __main__ import s')
# Test involving a class
s = Stack2()

```

As shown, the closure version runs about 8% faster. Most of
that is coming from streamlined access to the instance
variables. Closures are faster because there’s no extra
self variable involved.

Raymond Hettinger has devised an even more diabolical variant
of this idea. However, should you be inclined to do something
like this in your code, be aware that it’s still a rather
weird substitute for a real class. For example, major features
such as inheritance, properties, descriptors, or class
methods don’t work. You also have to play some tricks to
get special methods to work (e.g., see the implementation
of _len_() in ClosureInstance).

Lastly, you’ll run the risk of confusing people who read your
code and wonder why it doesn’t look anything like a normal
class definition (of course, they’ll also wonder why it’s faster).
Nevertheless, it’s an interesting example of what can be
done by providing access to the internals of a closure.

In the big picture, adding methods to closures might have more
utility in settings where you want to do things like reset
the internal state, flush buffers, clear caches, or have some
kind of feedback mechanism.
