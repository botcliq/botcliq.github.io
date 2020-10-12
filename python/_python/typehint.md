---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: post
title: Python Type Hints
lesson: 01
---
Its new feature in python language. 
Python PEP 484 introduced type annotation notation types in Python 3.
*Type hinting is literally what the words mean, you hint the type of the object(s) you're using.*

**In terms of style, PEP 8 recommends the following syntax:**

	1. Use normal rules for colons, that is, no space before and one space after a colon (text: str).
	2. Use spaces around the = sign when combining an argument annotation with a default value (align: bool = True).
	3. Use spaces around the -> arrow (def headline(...) -> str).

### Why Type Hints?
	1. Helps Type Checkers: 
	 By hinting at what type you want the object to be the type checker can easily detect if, for instance, you're passing
	 an object with a type that isn't expected.
	2. Helps with documentation: 
	 A third person viewing your code will know what is expected where, ergo, how to use it without getting them TypeErrors.
	3. Helps IDEs develop more accurate and robust tools: 
	 Development Environments will be better suited at suggesting appropriate methods when know what type your object is. You
	 have probably experienced this with some IDE at some point, hitting the . and having methods/attributes pop up which 
	 aren't defined for an object.

### Why use Static Type Checkers?
	1. Find bugs sooner: 
	 This is self evident, I believe.
	2. The larger your project the more you need it: 
	 Again, makes sense. Static languages offer a robustness and control that dynamic languages lack. The bigger and more 
	 complex your application becomes the more control and predictability (from a behavioral aspect) you require.

## Variables
> \# This is how you declare the type of a variable type in Python 3.6
> age: int = 1
> 
> \# In Python 3.5 and earlier you can use a type comment instead
> \# (equivalent to the previous definition)
> age = 1  # type: int
> 
> \# You don't need to initialize a variable to annotate it
> a: int  # Ok (no value at runtime until assigned)
> 
> \# The latter is useful in conditional branches
> child: bool
> if age < 18:
>     child = True
> else:
>     child = False
> '''

## Functions
Python support typ annotaions in function too.
> 
> from typing import Callable, Iterator, Union, Optional, List
> 
> \# This is how you annotate a function definition
> def stringify(num: int) -> str:
>     return str(num)
> 
> \# And here's how you specify multiple arguments
> def plus(num1: int, num2: int) -> int:
>     return num1 + num2
> 
> \# Add default value for an argument after the type annotation
> def f(num1: int, my_float: float = 3.5) -> float:
>     return num1 + my_float
> 
> \# This is how you annotate a callable (function) value
> x: Callable[[int, float], float] = f
> 
> \# A generator function that yields ints is secretly just a function that
> \# returns an iterator of ints, so that's how we annotate it
> def g(n: int) -> Iterator[int]:
>     i = 0
>     while i < n:
>         yield i
>         i += 1
> 
> \# You can of course split a function annotation over multiple lines
> def send_email(address: Union[str, List[str]],
>                sender: str,
>                cc: Optional[List[str]],
>                bcc: Optional[List[str]],
>                subject='',
>                body: Optional[List[str]] = None
>                ) -> bool:
>     ...
> 
> \# An argument can be declared positional-only by giving it a name
> \# starting with two underscores:
> def quux(__x: int) -> None:
>     pass
> 
> quux(3)  # Fine
> quux(__x=3)  # Error

## Decorators
Decorator functions can be expressed via generics. See Declaring decorators for the more details.
Type hints can be used in decorators as given below.
> from typing import Any, Callable, TypeVar
> 
> F = TypeVar('F', bound=Callable[..., Any])
> 
> def bare_decorator(func: F) -> F:
>     ...
> 
> def decorator_args(url: str) -> Callable[[F], F]:
>     ...

