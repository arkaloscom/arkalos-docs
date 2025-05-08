---
title: Multiple Route Files in Python FastAPI
description: How to organize and use multiple route files in Python FastAPI app. 
author: Mev-Rael
---

# Multiple Route Files in Python FastAPI

Arkalos HTTP server runs on top of **Uvicorn** and uses an extended **FastAPI** app for handling HTTP routes.

In an Arkalos project you may have both frontend and backend parts, or just use Arkalos for backend API as a microservice.

The default frontend setup is React + Vite + React Router (RR7), located in the `frontend` folder. You can replace this with your own frontend, remve it or keep it.

This guide focuses on the Python backend — specifically, how to define **API routes** using multiple files.



## What Are Routes?

Routes are simply URLs — like `/` or `/products/123` — that return data to your frontend or another service. Each route is linked to a Python function that handles the request and returns data.

Arkalos uses a modern Three-Tier Architecture, or a familiar structure similar to MVC (Model–View–Controller). Think of routes as your "controllers" or "actions", functions you call on a server when a request arrives.



## Organizing Routes in Files

All your route files live inside `app/http/routes/` folder.

Each file typically matches one feature or domain — like `reports.py`, `auth.py`, or `users.py`.

> [!NOTE]
> Arkalos automatically finds and installs all routes in this folder. No need to import or register them manually.



## A Simple Route Example

Let's say you want to show reports about the data you analyzed. Create a new route file `app/http/routes/reports.py`:

```python title="app/http/routes/reports.py"
from arkalos import router

@router.get('/reports')
async def reports():
    return {'message': 'Hi from Arkalos API endpoint!'}
```



## Adding Data to Routes

Let's say you want to return a dataset, like one from a Jupyter Notebook:

```python title="app/http/routes/reports.py"
from vega_datasets import data as datasets # type: ignore
import polars as pl
from arkalos import router

@router.get('/reports')
async def reports():
    cars_df = datasets.cars()
    df = pl.from_pandas(cars_df)
    return df.write_json()
```

> [!NOTE]
> All backend routes must return **JSON-serializable** data — typically a dictionary or JSON-like structure. This makes them compatible with frontend apps and fetch calls.



## Running the Server and Testing

Make sure your server is running:

```bash
uv run arkalos serve
```

Then to test your API endpoints, visit your API in the browser: `http://127.0.0.1:8000/api/reports`.

> [!NOTE] 
> All backend routes are prefixed with `/api`

You can also test using tools like Postman or your browser’s DevTools.
If you're using Edge, check out [Network Console](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/network-console/network-console-tool).



## Routes with Parameters

Routes, like functions, can accept parameters from the request.

There are HTTP methods, such as GET and POST. GET is the default method used to retrieve the data, and POST — to submit the form.

GET has two ways of dealing with parameters:



### Using Query Parameters (GET)

First. Search query parameters are commonly used on the same page to filter the data on that page.

Want to filter data with URL parameters like `?origin=japan`?

```python title="app/http/routes/reports.py"
from vega_datasets import data as datasets # type: ignore
import polars as pl
from arkalos import router

@router.get('/reports')
async def reports(origin: str | None = None):
    cars_df = datasets.cars()
    df = pl.from_pandas(cars_df)

    if origin:
        df = df.filter(pl.col('Origin').str.to_lowercase() == origin.lower())

    return df.write_json()
```

Visit:
`http://127.0.0.1:8000/api/reports?origin=japan`



### Using Path Parameters (GET) & 404 Response

Second. Paths are usually used for different pages or categories.

Want the parameter inside the path itself, like `/reports/japan`?

```python title="app/http/routes/reports.py"
from vega_datasets import data as datasets # type: ignore
import polars as pl

from arkalos import router
from arkalos.http import response

@router.get('/reports/{origin}')
async def reports_by_origin(origin: str):
    cars_df = datasets.cars()
    df = pl.from_pandas(cars_df)
    df = df.filter(pl.col('Origin').str.to_lowercase() == origin.lower())

    if df.is_empty():
        return response('Category not found', 404)

    return df.write_json()
```

Visit:
`http://127.0.0.1:8000/api/reports/japan`

In the case of the path, we use `response(data, status_code)` helper function to return 404 Page not Found response if the category is not found.



### Using POST & FormData Parameters

POST is used when sending data, like a form submission, such as a chat message.

You define a data class to receive the form data:

```python title="app/http/routes/reports.py"

from fastapi import Form, Depends

from arkalos import router

@dataclass
class ChatRequest:
    message: str = Form(...)

@router.post('/reports/chat')
async def reports_chat(request: ChatRequest = Depends()):
    print('User said: ', request.message)
    # Here you could trigger an AI agent or save to DB
```

Another example:

```python
@dataclass
class Report:
    title: str = Form(...)
    description: str = Form(...)

@router.post('/reports/create')
async def reports_create(report: Report = Depends()):
    print('Creating report:', report.title)
    # Save to database or process
```

---

## Error Handling and 500 Response

> [!NOTE] 
> Arkalos automatically catches errors and returns 500 response in a clear JSON format:

```python
@router.get('/reports-error')
async def reports_error():
    raise Exception("Something went wrong")
```

Response:

```json
{"error": "Something went wrong"}
```

Or a code mistake:

```python
@router.get('/reports-error')
async def reports_error():
    a  # Will return the error message with NameError
```

> [!NOTE]
> Errors and info logs are saved in `data/logs/` folder in JSONL (JSON Lines) format.
> You can view them in the **Frontend UI → Logs Page**, or in your terminal during development.

