# App and Modules

Writing app code is more than just coding — it's a **mindset**. It’s about designing code that’s easy to grow, share, reuse and maintain.

When you're writing **Code to Run**, like a notebook for quick data analysis or migration, you focus on getting the task done. But when writing **Code to Reuse**, you need to design it so it’s easy for you and others — like your teammates, teachers, managers, or professors — to integrate into scripts and notebooks.

Reusable code should avoid direct outputs like `print()` or charts. Instead, return values that can be used elsewhere. For example, if you generate a chart, consider returning it as an encoded string, making it flexible for various uses.

Think of reusable code like Lego blocks — modular pieces that fit together seamlessly.

## Modules and Packages

In Python, app code revolves around two key concepts: **Modules** and **Packages**.

## Creating Modules

A **module** is any Python file (`.py`) that contains definitions — functions, classes, or constants — that you can import into scripts or notebooks.

Previously, we created this module:

```python title="app/utils/my_utils.py"
from arkalos import config

def greet(greeting: str) -> None:
    print(f"{greeting}, {config('app.name')}")
```

Now we can rewrite it to follow the **Code to Reuse mindset**:

```python title="app/utils/my_utils.py"
from arkalos import config

def greet(greeting: str) -> str:
    return f"{greeting}, {config('app.name')}"
```

Notice how `greet()` now returns a string instead of printing it. This makes the function more versatile. You can print the result if needed:

```python
print(greet('Hi'))
```

Or compose it with other functions:

```python title="app/utils/my_utils.py"
from arkalos import config

def greet(greeting: str) -> str:
    return f"{greeting}, {config('app.name')}"

def bye(hello_message: str, goodbye_message: str) -> str:
    return f"{hello_message}. {goodbye_message}"
```

Now, use these in a notebook:

```python title="notebooks/my_notebook.ipynb"
from app.utils.my_utils import greet, bye

print(bye(greet('Hi'), 'Goodbye!'))
# Output: Hi, <your app name>. Goodbye!
```

By designing functions to return values, you make them flexible building blocks for larger workflows.

## Creating Packages

A **package** is a folder containing modules and an optional `__init__.py` file. The `__init__.py` file helps organize imports, making it easier to access functions.

For example, let’s create a package:

1. Create the folder structure:

    ```
    app/utils/my_package/
        __init__.py
        module_one.py
        module_two.py
    ```

2. Add functions to the modules:

    ```python title="app/utils/my_package/module_one.py"
    def add(a: int, b: int) -> int:
        return a + b

    def subtract(a: int, b: int) -> int:
        return a - b
    ```

    ```python title="app/utils/my_package/module_two.py"
    def multiply(a: int, b: int) -> int:
        return a * b

    def divide(a: int, b: int) -> float:
        return a / b
    ```

3. Organize imports in `__init__.py`:

    ```python title="app/utils/my_package/__init__.py"
    from app.utils.my_package.module_one import add, subtract
    from app.utils.my_package.module_two import multiply, divide
    ```

Now, you can import the whole package easily:

```python title="notebooks/my_notebook.ipynb"
from arkalos import var_dump
import app.utils.my_package as my_package

var_dump(my_package.add(5, 3))       # Output: int(8)
var_dump(my_package.divide(10, 2))   # Output: float(5.0)
```

No need to import each module individually — your package handles that for you!

> [!NOTE]
> External packages (installed via `uv add <package>`) are more complex and aren’t covered here. Publishing your own external packages involves additional steps like using a `src/` folder and registering with PyPI.

## Functions vs Classes

## When to Use Functions

Start with **functions** for simple, reusable tasks. Good functions:

- Take simple inputs
- Return simple outputs
- Do one thing well
- Avoid side effects (don’t modify external states)

Keep functions short and focused.

## When to Use Classes

Use **classes** when you need to define custom data types or enforce structure in your code.

## Classes for Custom Data Types

If you need to represent a structured data, use the `@dataclass` decorator.

A Python decorator is like a magical spell you can put on a function or a class to change how it works without changing the function itself. A decorator is like adding a honking device to the car without touching its engine!

A dataclass decorator is like a special kind of toy box where you can put different toys (data) in an organized way.

```python title="app/data/types/point.py"
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

```

```python title="notebooks/my_notebook.ipynb"
from arkalos import var_dump

from app.data.types.point import Point

my_point = Point(3, 5)
var_dump(my_point)
```

This defines a custom `Point` data type.

You can pass data in this format as arguments of functions across your workflows.


## Classes for Contracts (Interfaces)

To enforce a consistent structure, use **abstract classes** as contracts (also called interfaces).

```python title="app/utils/greeter.py"
from abc import ABC, abstractmethod

class Greeter(ABC):
    @abstractmethod
    def myAction(self, greeting: str) -> str:
        pass
```

`Greeter` is an abstract base class. It defines the method `myAction`, but doesn’t implement it.

Now, implement this contract in a concrete class:

```python title="app/utils/my_greeter.py"
from app.utils.greeter import Greeter

class MyGreeter(Greeter):
    def myAction(self, greeting: str) -> str:
        return f"{greeting}, from MyGreeter!"
```

And use it in your notebook:

```python title="notebooks/my_notebook.ipynb"
from app.utils.my_greeter import MyGreeter

greeter = MyGreeter()
print(greeter.myAction('Hello'))
# Output: Hello, from MyGreeter!
```

Using abstract classes ensures that everyone on your team follows the same structure, making your codebase more consistent and easier to maintain.


---

## What Next

Now that you know how to structure your app code with modules, packages, functions, and classes, you’re set to build reusable, maintainable Python projects and learn about [Working as a Team](/docs/teamwork)

