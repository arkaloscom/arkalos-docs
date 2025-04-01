# Registry and Bootstrapping

## Understanding the Registry

A **Registry** is like a simple table that helps your code quickly find and use the right functions, classes, or modules. It’s similar to a **library catalog** — you look up a book by its title and find out where to get it.

Think of it this way:

- The **first column** is a short key (like a book title).
- The **second column** is where to find the book (who has it, or which shelf it’s on).

When you need a book, you don’t care who has it at that moment — you just use the title to get it. Similarly, in coding, you don’t always care which database or module is being used behind the scenes. You just ask for what you need, and the Registry finds the right tool for the job.

The advantage? You can **swap out** one tool for another without rewriting your entire codebase — just like borrowing the same book from a different library branch.

---

The concept of Registry and retrieving dependencies dynamically is widely used across multiple fields under different names, e.g: Lookup Table, Dynamic Linker, Feature Store, Dependency Injection (DI), Service Container, Factory, Inventory System, Ledger, Dynamical System and Dynamic State Selection, Neural Memory Retrieval, Schema Theory, Theory of Forms, and Category Theory.


## Getting Modules from the Registry

In Arkalos, you can retrieve functions and modules from the Registry using a simple command:

```python
from arkalos import Registry

config_func = Registry.get('config')
config_func('app.name')
```

This is the same as doing:

```python
from arkalos import config
config('app.name')
```

## Why use the Registry?
Using a Registry allows for flexibility. The `config()` function itself retrieves the actual configuration function from the Registry before running it. This means you can **replace** the default `config()` function with your own custom version when needed.

For example, let’s say you want to override the built-in configuration system with your own:

```python
from typing import Optional

from arkalos import Registry

def my_config(key: str, default: Optional[str]=None) -> str:
    return f"Custom config for {key}"

Registry.register('config', my_config)

from arkalos import config

# Now this will return our custom message instead of the default behavior
print(config('app.name'))  # Output: Custom config for app.name
```

With this approach, any script using `config('app.name')` will now call your custom function instead of the default one.



## Registering Classes

Arkalos comes with a few other utility functions that retrive the same object of the same class and a contract.

```python
from arkalos import Log, dwh
```

`Log` is a module wrapper around the Logger class that comes from the Registry.

`dwh()` is a simple helper function that allows you to retrive the data warehouse from any part of your application or a script.

```python
print(dwh().NAME)    # SQLite
```

You can register classes the same way:

```python
from arkalos import Registry

class MyClass:
    def myAction(self):
        print('My action')

Registry.register('my_component', MyClass)

def my_component():
    return Registry.get('my_component')

my_component().myAction()
```

## Bootstrapping: Setting Up Your Project

**Bootstrapping** is the process of setting up and starting your application — just like how a computer boots up when you turn it on.

When you develop an app or run scripts frequently, you might want to add **global setup logic** that runs **every single time** before anything else. For example, if your app depends on a database or external service, you can configure it at the start.

Arkalos includes a default file for this: `app/bootstrap.py`. This file acts as a global setup script.

Here’s what it looks like by default:

```python title="app/bootstrap.py"
from arkalos import Registry

def run():
    pass  # You can register custom modules here
    # Registry.register('my_module', my_module)
```

### How to Use Bootstrapping
Before running any script, you can **bootstrap** your project by adding this at the top of your file:

```python
import app.bootstrap as bootstrap
bootstrap.run()
```

This ensures all necessary configurations and modules are properly set up before the rest of your script runs.




## Why This Matters

Using a Registry and Bootstrapping helps keep your code **organized, flexible, and scalable**. With these tools, you can:

- **Easily swap out** modules or functions without rewriting code.
- **Set up global settings** that apply across your entire project.
- **Keep your scripts clean** by handling setup logic in one place.


## What Next

Congratulations! You have completed the **Writing Basic Code** guide and can start writing Arkalos code!

You may explore the next section about [Data Sources & Extractors](/docs/data-sources)
