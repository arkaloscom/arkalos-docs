---
title: Python FastAPI Domains, Models & Controllers
description: Learn how to structure your Python FastAPI project using domain-driven design, understand key data modeling concepts, and differentiate between OLTP and OLAP systems.
author: Mev-Rael
---

# Python FastAPI Domains, Models & Controllers

Multitier software architecture and design patterns help us write simple, maintainable, testable, and easily adjustable and scalable code.

Arkalos provides a default structure for various tiers of your architecture that are similar across different types of software.

However, the business logic or any use-case-specific and domain knowledge is unique to every project. Arkalos doesn't dictate where you should create your models, domains, or controllers, or even if you need them.

That said, Arkalos includes an example domain class in `app/domains/example/example.py`, which uses the `vega_datasets` library to generate some example charts for the initial dashboard. You can delete or adjust this as you see fit.

## Where to Put Business Logic?

If your application is small, you may use or create directories such as `app/models`, `app/data/types`, `app/http/actions`, and `app/http/controllers` to house your models and business-specific code. Then, import and use them from your routes.

If you plan to have multiple use-cases, more sections and domains of knowledge, or better organize your codebase—where all the models, data types, actions or controllers, and other code related to a particular domain live inside the same directory like an independent sub-project—then you may stick to the `app/domains` folder.

## About Domain-Driven Design

**Domain-Driven Design (DDD)** is a standard approach in professional application design that helps us understand the particular knowledge domain and its boundaries, and better structure our code to meet the always-changing requirements.

Each domain will have its own directory, and then each directory will have a similar structure. For instance, each domain might have some of these folders:

- `app/domains/{domain}/`
- `app/domains/{domain}/actions/`
- `app/domains/{domain}/models/`
- `app/domains/{domain}/types/`

Each domain must be treated as an absolutely independent project. Only the domain itself knows about its structure and internals. Other domains should never access deeper internal parts of other domains, but instead access it only via the entry point—like the main gates to a palace—and then the domain itself provides what is requested. Think of it as object-oriented programming (OOP) but at a larger scale, where entire folders and files are like private methods.

> [!NOTE]
> This entry point is often known as an **Aggregate Root**, and usually takes the form of a class with the same name as the domain and folder name. `app/domains/example/example.py` is an example of that root.

Designing domains and naming our folders, files, and entities requires at least a basic understanding of the domain and close collaboration with domain experts. This helps the entire team and organization to align, enabling both technical and non-technical people to speak the same language.

This also helps to decouple your architecture and, if needed in the future, to easily separate your codebase into multiple repositories and microservices.

## Understanding Data Models, Data Types, and AI Models

### What is a Data Model?

A **data model** is a conceptual representation or structure that defines how data is organized, stored, and manipulated within a system or application. It specifies the relationships between different data entities, the attributes of those entities, and the rules governing the data.

In the context of Arkalos, a data model represents business data and can take various forms:

- A custom class
- A table in the database
- A simple static model or dictionary (e.g., a list of US states)

It serves as the interface between the application and domain language and the raw data storage. For example, we might have a `User` model with a corresponding `user` table in the database and a `UserType` data type. We could easily add a new user with a `User.create()` method or fetch only admin users with `User.admins()`.

This abstraction allows us to replace the underlying data storage without rewriting our entire codebase.

### What is a Data Type?

A **data type** or **data transfer object (DTO)** is a custom data structure for a particular model, request, or any use case. It defines the shape and type of data being handled, ensuring consistency and validation across the application.

For example, using Python's `dataclasses` or Pydantic's `BaseModel`, we can define data types that validate incoming data and provide clear documentation for the expected structure.

### What is an AI Model?

An **AI model** is a computational representation of a real-world process or problem that can be learned from data. AI models are created using machine learning algorithms and techniques to analyze and learn patterns from training data. These models are then used to make predictions or decisions on new, unseen data.

Examples of AI models include:

- Linear regression
- Decision trees
- Support vector machines
- Neural networks

In Arkalos, AI models can be integrated into your domains to provide intelligent features, such as recommendations, predictions, or classifications.

This guide focuses only on the data models and types.



## About Data Warehouses, Data Products, OLAP, and OLTP

When building **data products**—such as pipelines, analytics, reporting, automations, business intelligence (BI) tools, smart and AI engines, dashboards, and data warehouses—it's important to separate the core business domain and requirements from the design of the data structure and flows that are independent from the business operations.

### OLTP (Online Transaction Processing)

In typical products and small applications, you usually design a single database and focus on transactional data, known as OLTP. OLTP systems are optimized for managing current day-to-day data information, handling large numbers of short online transactions (INSERT, UPDATE, DELETE). They emphasize fast query processing and maintaining data integrity in multi-access environments.

### OLAP (Online Analytical Processing)

When designing data products, data warehouses, and the entire infrastructure, the approach is different and is known as OLAP. OLAP systems are designed for complex queries and data analysis, enabling businesses to derive insights from vast datasets through multidimensional analysis.

For example, in OLTP and your main database, you care less about historical data and logs, and store all kinds of information in a normalized way. This, however, might not be the optimal approach when it comes to reading historical data.

### Data Warehouse

A **data warehouse** is a kind of database that:

- Connects multiple data sources, such as your main OLTP product database, CRM, project management tools, social media analytics, and so on.
- Provides historical data.

Data warehouses and data pipelines often access various data sources via polling. For example, your main product and database is a task management tool, and there is a `tasks` table with a `status` column. But anytime the status is changed, you have no historical record of that.

A data warehouse could access this table every hour to retrieve the new and updated data and store changes in the data itself over time.

Historical data is a cornerstone of analytics and data-driven business decisions and intelligence.

There are two common classical ways of designing a data warehouse:

- **Inmon approach**: Focuses on building a centralized data warehouse first, then creating data marts for specific business areas.
- **Kimball approach**: Emphasizes building data marts first, which are then integrated into a data warehouse.

## What is a Controller and Action?

An **action** is an atomic unit of operation that a domain or the project can perform and exposes these actions to the user.

Domains can have domain actions.

A **controller** is a general term and refers to any part of the code responsible for controlling and connecting different pieces of code together. For example, AI agents are controllers, HTTP routes are controllers.

Controllers are useful when you need to group many actions and routes together. Even if we have multiple route files in the `app/http/routes` directory, when a particular page or domain grows, we don't want our route functions to become too large. Instead, we can delegate this to a controller or an action.

Controllers are often used in simple MVC-type applications. With domains, it's recommended to use domain actions instead because the aggregate root is our controller.




## Creating a Domain

Let's refactor our previous example from the Routes guide about the Reports.

Create `app/domains/report/` folder.

### Data Types

Now let's create a new `app/domains/report/types/` folder and new files and data types:

```python title="app/domains/report/types/car_origin_type.py"

from enum import StrEnum

class CarOriginType(StrEnum):
    US = 'usa'
    JAPAN = 'japan'
    EU = 'europe'
```

```python title="app/domains/report/types/car_report_type.py"

import datetime
from pydantic import BaseModel, model_validator

from app.domains.report.types.car_origin_type import CarOriginType

class CarReportType(BaseModel):
    Name: str
    Miles_per_Gallon: float
    Cylinders: int
    Displacement: float
    Horsepower: float
    Weight_in_lbs: int
    Acceleration: float
    Year: datetime.date
    Origin: CarOriginType

    @model_validator(mode='after')
    def check_mph_cylinders_ratio(self) -> 'CarReportType':
        '''As the cylinder count increases, average MPG usually decreases'''
        mpg = self.Miles_per_Gallon
        cylinders = self.Cylinders
        if mpg > 50 and cylinders > 6:
            raise ValueError("High MPG with many cylinders is suspicious")
        return self
```

We then in our code could use this class to easily create a new data or validate it:

```python title="notebooks/car_report.ipynb"
new_cars = [
    CarReportType(
        Name="Pydantic Test Car 1",
        Miles_per_Gallon=30.5,
        Cylinders=4,
        Displacement=140.0,
        Horsepower=90.0,
        Weight_in_lbs=2500,
        Acceleration=15.0,
        Year=datetime.date(2025, 1, 1),
        Origin=OriginType.JAPAN
    ),
    CarReportType(
        Name="Pydantic Test Car 2",
        Miles_per_Gallon=22.0, # Set this to > 50
        Cylinders=6, # and this to > 6 to test our custom data analysis validation
        Displacement=200.0,
        Horsepower=110.0,
        Weight_in_lbs=3000,
        Acceleration=12.5,
        Year=datetime.date(2025, 1, 1),
        Origin=OriginType.US
    )
]

report = CarReport(new_cars)
report.filterByOrigin(OriginType.US)
df = report.getPolarsDf()
df
```

> [!NOTE]
> Data types help us see which exact columns, column names, data and types we are dealing with. It helps with alignment and communication, and also acts as a documentation and a certain safety check or custom validators for data analysis that could warn us if something looks suspicious, or if something is just missing or not valid.


### Data Models

Now let's create the main data model. In a real scenario we would have to design a DB or new data structure to store our data, but we will continue in this example with the vega_datasets.

Create `app/domains/report/models/` folder and `app/domains/report/models/car_report.py` model

```python title="app/domains/report/models/car_report.py"

from vega_datasets import data as datasets # type: ignore
import polars as pl

from app.domains.report.types.car_report_type import CarReportType

class CarReport:

    df: pl.DataFrame

    def __init__(self, data: list[CarReportType]|None = None):
        if data:
            self.df = pl.DataFrame(data)
        else:
            cars_df = datasets.cars()
            self.df = pl.from_pandas(cars_df)

    def getPolarsDf(self) -> pl.DataFrame:
        return self.df

    def isEmpty(self) -> bool:
        return self.df.is_empty()

    def filterByOrigin(self, origin: str|None = None):
        if origin:
            self.df = self.df.filter(pl.col("Origin").str.to_lowercase() == origin.lower())

    def getJSON(self) -> str:
        return self.df.write_json()
```



### Domain Actions

Now let's create domain actions that we want to expose outiside of this domain, i.e. to our HTTP routes and the user.

Create a new `app/domains/report/actions/` folder and `get_car_report_action.py` file inside:

```python title="app/domains/report/actions/get_car_report_action.py"

from app.domains.report.types.car_origin_type import CarOriginType
from app.domains.report.models.car_report import CarReport

class GetCarReportAction:

    def __call__(self, origin: CarOriginType|None = None) -> str|bool:
        '''Return report as JSON data or False if no records found for car origin type'''
        report = CarReport()
        report.filterByOrigin(origin)
        if report.isEmpty():
            return False
        return report.getJSON()

```

### Domain Aggregate Root

Finally, let's create the main aggregate and domain entry class `app/domains/report/report.py`

```python title="app/domains/report/report.py"

from app.domains.report.types.car_origin_type import CarOriginType
from app.domains.report.actions.get_car_report_action import GetCarReportAction

class Report:

    def getCarReport(self, origin: CarOriginType|None = None) -> str|bool:
        '''Return report as JSON data or False if no records found for car origin type'''
        action = GetCarReportAction()
        return action(origin)
```

And we can now use it in our `app/http/routes/report.py` route and refactor it:

Using search query params:

```python title="app/http/routes/reports.py"

from arkalos import router

from app.domains.report.report import Report
from app.domains.report.types.car_origin_type import CarOriginType

@router.get('/reports')
async def reports(origin: CarOriginType|None = None):
    domain = Report()
    data = domain.getCarReport(origin)

    if not data:
        return []

    return data
```

Or using path params:

```python title="app/http/routes/reports.py"

from arkalos import router
from arkalos.http import response

from app.domains.report.report import Report



@router.get('/reports')
async def reports():
    domain = Report()
    return domain.getCarReport()

@router.get('/reports/{origin}')
async def reports_category(origin: str):
    domain = Report()
    data = domain.getCarReport(origin)

    if not data:
        return response('Category not found', 404)

    return data
```

And it all is now much cleaner, better organized and we can scale this easily and our routes look clean and short. We adhere to the principle of single responsibility.
