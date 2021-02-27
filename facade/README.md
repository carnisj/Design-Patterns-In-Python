# Facade Design Pattern

## Overview

Sometimes you have a system that becomes quite complex over time as more features are added or modified. It may be useful to provide a simplified API over it. This is the facade pattern.

The facade pattern essentially is an alternative, reduced or simplified interface to a set of other interfaces, abstractions and implementations within a system that may be full of complexity and/or tightly coupled.

It can also be considered as a higher-level interface that shields the consumer from the unnecessary low-level complications of integrating into many subsystems.

## Facade UML Diagram

![Facade Design Pattern](/img/facade_concept.svg)

## Output

``` bash
python ./facade/facade_concept.py
A
B
{'C': [1, 2, 3]}
A
B
{'C': [1, 2, 3]}
```

## Example Use Case

This is an example of a game engine API. The facade layer is creating one streamlined interface consisting of several methods from several larger API backend systems. 

The client could connect directly to each subsystems API and implement its authentication protocols, specific methods, etc. While it is possible, it would be quite a lot of consideration for each of the development teams, so the facade API unifies the common methods which becomes much less overwhelming for each new the client developer to integrate into.

## Example UML Diagram

![Facade Example UML Diagram](/img/facade_example.svg)

## Output

``` bash
python ./facade/client.py     

---- Gamestate Snapshot ----
{'clock': 59, 'game_open': True, 'entries': [('sean', Decimal('5'))]}

---- Reports History ----
0 : 1614087127.327007 : new user `sean` created
1 : 1614087127.327007 : wallet for `sean` created and set to 0
2 : 1614087127.327007 : Give new user `sean` sign up bonus of 10
3 : 1614087127.327007 : Balance adjustment for `sean`. New balance = 10
4 : 1614087128.3278701 : Balance check for `sean` = 10
5 : 1614087128.3278701 : Balance adjustment for `sean`. New balance = 9
6 : 1614087128.3278701 : New entry `5` submitted by `sean`

---- Gamestate Snapshot ----
{'clock': 58, 'game_open': True, 'entries': [('sean', Decimal('5'))]}
```

## New Coding Concepts

### Python `decimal` Module

The `decimal` module provides support for correctly rounded decimal floating-point arithmetic.

If representing money values in python, it is better to use the `decimal` type rather than `float` .

Floats will have rounding errors versus decimal.

``` python
from decimal import Decimal

print(1.1 + 2.2)  # adding floats
print(Decimal('1.1') + Decimal('2.2')) # adding decimals
```

Outputs

``` 

3.3000000000000003
3.3
```

Note how the float addition results in `3.3000000000000003` whereas the decimal addition result equals `3.3` .

Be aware though that when creating decimals, be sure to pass in a string representation, otherwise it will create a decimal from a float.

``` python
from decimal import *

print(Decimal(1.1))  # decimal from float
print(Decimal('1.1'))  # decimal from string
```

Outputs

``` 

1.100000000000000088817841970012523233890533447265625
1.1
```

Python Decimal: [https://docs.python.org/3/library/decimal.html](https://docs.python.org/3/library/decimal.html)

### Type Hints

In the Facade use case example, I have added type hints to the method signatures and class attributes.

``` python
    _clock: int = 0
    _entries: list[tuple[str, Decimal]] = []

    ...

    def get_balance(user_id: str) -> Decimal:
        "Get a players balance"
        ...

    ...

    def register_user(cls, new_user: dict[str, str]) -> str:
        "register a user"
        ...

```        

See the extra `: str` after the `user_id` attribute, and the `-> Decimal` before the final colon in the `get_balance()` snippet.

This is indicating that if you use the `get_balance()` method, that the `user_id` should be a type of `string`, and that the method will return a `Decimal` .

Note that the Python runtime does not enforce the type hints and that they are optional. However, where they are beneficial is in the IDE of your choice or other third party tools such type checkers. 

In VSCode, when typing code, it will show the types that the method needs.

![VSCode Type Hints](/img/ide_hint.jpg)

For type checking, you can install an extra module called `mypy`

``` bash
pip install mypy
```

and then run it against your code, 

``` bash
mypy ./facade/client.py
Success: no issues found in 1 source file
```

Mypy will also check any imported modules at the same time. 

If working with money, then it is advisable to add extra checks to your code. Checking that type usage is consistent throughout your code, especially when using Decimals, is a good idea that will make your code more robust.

For example, if I wasn't consistent in using the Decimal throughout my code, then I would see a warning highlighted.

``` bash
mypy ./facade/client.py  
facade/game_engine.py:45: error: Argument 1 to "append" of "list" has incompatible type "Tuple[str, int]"; expected "Tuple[str, Decimal]"
facade/game_api.py:34: error: Argument 2 to "submit_entry" of "GameEngine" has incompatible type "Decimal"; expected "int"
Found 2 errors in 2 files (checked 1 source file)
```

## Summary

* Use when you want to provide a simple interface to a complex subsystem.
* You want to layer your subsystems into an abstraction that is easier to understand.
* [Abstract Factory](abstract_factory) and Facade can be considered very similar. An Abstract Factory is about creating in interface over several creational classes of similar objects, whereas the Facade is more like an API layer over many creational, structural and/or behavioral patterns. 
* The [Mediator](mediator) is similar to the Facade in the way that it abstracts existing classes. The Facade is not intended to modify, load balance or apply any extra logic. A subsystem does not need to consider that existence of the facade, it would still work without it.
* A Facade is a minimal interface which could also be implemented as a [Singleton](singleton).
* A Facade is an optional layer that does not alter the subsystem. The subsystem does not need to know about the Facade, and could even be used by many other facades created for different audiences.