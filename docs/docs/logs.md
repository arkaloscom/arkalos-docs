# Logs and Debugging

## Debugging

To identify issues in your code, start by adding `print()`, `var_dump()`, or `dd()` functions. These help inspect variable values and data types during execution.

When you are dealing with errors and exceptions, you can also wrap your code in `try - except` block.

Instead of manually commenting out large sections of code, use the `dd()` function to halt execution at a specific point:

```python
from arkalos import dd

def calculate_square(x):
    result = x * x
    dd(result)  # This will print the result and stop the script
    return result

def main():
    number = 5
    square = calculate_square(number)
    # The following line won't run due to dd()
    print(f"The square of {number} is {square}")

main()
```



## Logging

Arkalos includes a pre-configured file-based logger for easy tracking of events:

```python
from arkalos import Log

Log.debug('debug message')
Log.info('info message')
Log.warning('warning message')
Log.error('error message')
Log.critical('critical error')
```

This will generate a log file in `data/logs/arkalos-<year>-<month>.log`.

You can open it in VS Code to check the details, including the exact date and time in UTC (Zulu) timezone.



## Logging Data

To log additional data, pass it as a second argument:

```python
from arkalos import Log

data = {
    'id': 1,
    'tags': ['one', 'two'],
}

Log.info('info message', data)
```

Logging allows you to review execution details in a log file, making it easier to debug issues.



## What Next

Now you can learn about the [Registry & Bootstrapping](/docs/registry).
