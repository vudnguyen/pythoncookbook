#### Python Cookbook: Classes & Objects | Step 2 of 6

# Customizing String Formatting

## Problem

---

You want an object to support customized formatting through
the format() function and string method.

## Solution

---

To customize string formatting, define the
**format**() method on a class.

<hr >

For example:

```

_formats = {
    'ymd' : '{d.year}-{d.month}-{d.day}',
    'mdy' : '{d.month}/{d.day}/{d.year}',
    'dmy' : '{d.day}/{d.month}/{d.year}'
    }

class Date(object):
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day
    def __format__(self, code):
        if code == '':
            code = 'ymd'
        fmt = _formats[code]
        return fmt.format(d=self)

```

Instances of the Date class now support formatting
operations such as the following:

```

$ d = Date(2012, 12, 21)
$ format(d)
$ format(d, 'mdy')
$ 'The date is {:ymd}'.format(d)
$ 'The date is {:mdy}'.format(d)

```

## Discussion

---

The **format**() method provides a hook into Python’s string
formatting functionality. It’s important to emphasize that
the interpretation of format codes is entirely up to the
class itself. Thus, the codes can be almost anything at all.
For example, consider the following from the datetime module:

```

from datetime import date
d = date(2012, 12, 21)
format(d)
format(d,'%A, %B %d, %Y')
'The end is {:%d %b %Y}. Goodbye'.format(d)

```

There are some standard conventions for the formatting of the
built-in types. See the documentation for the string
module for a formal specification.

https://docs.python.org/3/library/string.html

https://docs.python.org/3.7/
