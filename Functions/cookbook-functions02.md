#### Python Cookbook: Functions | Step 2 of 9

# Writing Functions That Only Accept Keyword Arguments

## Problem

---

You want a function to only accept certain arguments
by keyword.

## Solution

---

This feature is easy to implement if you place the
keyword arguments after a _ argument or a single
unnamed _ . For example:

```

def recv(maxsize, *, block):
    #'Receives a message'
    pass

#  Returns a TypeError
$ recv(1024, True)
# Ok
$ recv(1024, block=True)

```

This technique can also be used to specify keyword
arguments for functions that accept a varying number
of positional arguments.

For example:

```
def minimum(*values, clip=None):
    m = min(values)
    if clip is not None:
        m = clip if clip > m else m
    return m

$ minimum(1, 5, 2, -5, 10)          # Returns -5
$ minimum(1, 5, 2, -5, 10, clip=0) # Returns 0

```

## Discussion

---

Keyword-only arguments are often a good way to enforce greater
code clarity when specifying optional function arguments.
For example, consider a call like this:

\$ msg = recv(1024, False)

If someone is not intimately familiar with the workings of the recv(),
they may have no idea what the False argument means. On the other hand,
it is much clearer if the call is written like this:

\$ msg = recv(1024, block=False)

The use of keyword-only arguments is also often preferrable to tricks
involving \*\*kwargs, since they show up properly when the user asks for help:

help(recv)

Keyword-only arguments also have utility in more advanced contexts.
For example, they can be used to inject arguments into functions
that make use of the \*args and \*\*kwargs convention for accepting
all inputs.
