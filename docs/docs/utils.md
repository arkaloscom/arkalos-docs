# Utils

Utils are utility or helper functions that simplify coding tasks.

- Common utility functions should be placed in the `app/utils/` folder.
- Core functions, especially those related to extending the Arkalos framework, should go into the `app/core/` folder. For example, you can override the default `config()` function behavior by implementing your own version.

## Available Utils

All utility functions can be imported directly from Arkalos:

```python
from arkalos import config, env, dd, var_dump, base_path, get_data_schema
```

### Utility Functions

- `config('config_file.key')` - Retrieves a configuration value.
- `env('ENV_KEY')` - Fetches an environment variable.
- `var_dump(variable)` - Displays the contents of a variable for debugging.
- `dd(variable)` - Dumps a variable and stops execution.
- `base_path()` - Returns the string of the current base path to the project root. Optionally, can accept an argument.
- `get_data_schema(data) -> Polars Schema` - Infers data structure from the first rows of the data and returns polars.Schema object.

### `dd()` Function

The `dd()` function (short for "dump and die") calls `var_dump()`, but also halts script execution. It is useful for debugging and testing.


## Functional Programming (FP) Utils

Also can be directly imported from the Arkalos:

```python
from arkalos import partial, compose, pipe
```

- `partial(function, {args})` - **Partial Application** of a function where a new function is created and certain arguments are fixed. Great for composition or passing functions or classes as arguments into other functions while setting arguments to specific values. (it is a function from `functools` module)
- `compose(function1, function2, ...)` - Applies functions right to left.
- `pipe(function1, function2,  ...)` - Applies functions left to right.


## Debugging and Logs

Next, explore more debugging and logging tools in the [Logs & Debugging](/docs/logs) section.

