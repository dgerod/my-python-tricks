# My Python Tricks

I am working on Python 2.7 so it is possible that some trick does not work in Python 3, or maybe there are better options.

## Checking arguments and return types

Two decorators named as @accepts and @returns can be used to check types of functions and methods of a class. In case that an argument passes to the function or method is not from specified type a ValueError is raised, same thing for the return type. Decorators could be used independently.

Example:

```python
>>> @accepts(str, int)
... @returns(int)
... def test_function(a, b):
...   return 1
 
>>> test_function('hello', 100)
... 1

>>> class Test(object):
... @accepts(object, str, int)
... @returns(int)
... def test_method(a, b):
...   return 1
    
>>> t = Test()
... t.test_method('hello', 100)
... 1
```

Code:
```python  
def accepts(*args_types, **kwargs_types):
    """
    Decorator to check argument types.

    Usage:
        @accepts(str) for functions
        @accedef accepts(*args_types, **kwargs_types):
    """
    def wrapper(func):
        def wrapped(*args, **kwargs):
            if len(args) > len(args_types):
                raise TypeError("%s() takes at most %s non-keyword arguments (%s given)" % (func.__name__, len(args_types), len(args)))
            args_pairs = zip(args, args_types)
            for k,v in kwargs.items():
                if k not in kwargs_types:
                    raise TypeError("Unexpected keyword argument '%s' for %s()" % (k, func.__name__))
                args_pairs.append((v, kwargs_types[k]))
            for param, expected in args_pairs:
                if param is not None and not isinstance(param, expected):
                    raise TypeError("Parameter '%s' is not %s" % (param, expected.__name__))
            return func(*args, **kwargs)
        return wrapped
    return wrapper

def returns(return_type):
    """
    Decorator to check return type.

    Usage:
        @returns(int)
    """
    def wrapper(func):
        def wrapped(*args, **kwargs):
            result = func(*args, **kwargs)
            if not isinstance(result, return_type):
                raise TypeError("return value %r does not match %s" % (result, return_type))
            return result
        return wrapped
    return wrapper
```

References:
* [Type enforcement in Decorator Libary](https://wiki.python.org/moin/PythonDecoratorLibrary#Type_Enforcement_.28accepts.2Freturns.29)
* [Python check arguments types at Mathieu Leplatre's Blog](http://blog.mathieu-leplatre.info/python-check-arguments-types.html)

## Muliple dispatch

Python is a dynamically typed language, so the concept of overloading simply does not apply to it. However, it is possible to use multiple dispatch (aka multi mehtods) to simulat this behavior. Multiple dispatch is a feature of some OOP languages in which a function or method can be dynamically dispatched based on the run time type of its arguments.

Example:

```python

   >>> from multipledispatch import dispatch

   >>> @dispatch(int, int)
   ... def add(x, y):
   ...     return x + y

   >>> @dispatch(object, object)
   ... def add(x, y):
   ...     return "%s + %s" % (x, y)

   >>> add(1, 2)
   3

   >>> add(1, 'hello')
   '1 + hello'
```
   
References:
* [Understanding the multiple dispatch technique](https://en.wikipedia.org/wiki/Multiple_dispatch)
* [Multiple Dispatch package](https://github.com/mrocklin/multipledispatch)

## Others things
* [Github wiki syntax](https://help.github.com/categories/writing-on-github/)
