---
title: Python FastAPI Middleware Class
description: How to create and register middleware in your Python FastAPI project to run logic before and after every request.
author: Mev-Rael
---

# Python FastAPI Middleware Class

Middleware is like a security guard at the entrance to a mall. Most of the time, they simply observe people coming and going. But sometimes, like at airports, they inspect everyone passing through.

HTTP middleware works similarly — it wraps around every request and response cycle. Middleware can run logic:

- **Before** the request is processed,
- **After** the response is generated,
- Or **both** before and after.

Middleware is typically used for global services such as:

- Error handling
- Logging
- Authentication

Each middleware should live in its own class and focus on a single concern. This makes debugging easier and allows enabling or disabling middleware with minimal impact.



## Creating a New Middleware

In Arkalos, to add a new middleware, create a file in the `app/http/middleware/` folder and extend the base `Middleware` class:

```python title="app/http/middleware/my_logs_middleware.py"
from typing import Callable
from arkalos.http import Middleware, Request, Response

class MyLogsMiddleware(Middleware):

    async def __call__(self, request: Request, call_next: Callable) -> Response:
        # BEFORE: The request is available, but no response yet
        print(request.url.path)

        # Pass control to the next middleware or the route handler
        resp = await call_next(request)

        # AFTER: Response is available, can log status or modify it
        return resp
```

To create custom middleware, your class must:

* Inherit from `Middleware`
* Implement the `async __call__(self, request, call_next)` method
* Call `await call_next(request)` to continue the chain
* Return the final response

The example above prints the path of every request to the terminal.



## Registering Middleware

To enable your middleware, register it inside `app/bootstrap.py`:

```python title="app/bootstrap.py"
# ...

from app.http.middleware.my_logs_middleware import MyLogsMiddleware

# ...

def run():
    # ...

    app = (HTTP()
        .withBasePath(base_path())
        .withMiddleware([
            HandleExceptionsMiddleware,
            LogRequestsMiddleware,
            MyLogsMiddleware,  # 👈 Add your new middleware here
        ])
        .withRoutes([
            ('app/http/routes', '/api')
        ])
        .withMountFrontendBuildDir()
        .create()
    )

```

Middleware runs in the order they are listed. That means your middleware will see the request **after** the ones listed above it, and process the response **before** them.
