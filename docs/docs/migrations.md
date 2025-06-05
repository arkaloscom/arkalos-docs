---
title: Python DB & Data Warehouse Migrations
description: How to create and manage SQLite, DuckDB, or PostgreSQL database and data warehouse migrations with Python FastAPI. Includes examples using SQLGlot, Ibis, and an easy-to-use query builder.
author: Mev-Rael
---

# Python DB & Data Warehouse Migrations

Arkalos lets you manage schema changes in your relational database or data warehouse with simple, versioned Python migration scripts. Built on top of [SQLGlot](https://github.com/tobymao/sqlglot) and [Ibis](https://ibis-project.org/), Arkalos supports `CREATE TABLE`, `ALTER TABLE`, and other SQL operations using a Pythonic syntax.

This guide shows how to write, organize, and run database and data warehouse migrations in your FastAPI or data engineering project using Arkalos.

## Versioning Migrations

Arkalos organizes migrations by **version subfolders**, instead of storing many files in a single flat directory. This helps your team track schema changes logically alongside your app or warehouse version.

**Choose a versioning strategy and stick to it:**

* Semantic versioning: `0.1.0`, `0.2.0`, etc.
* Minimal versioning: `0.1`, `0.2`
* Date-based: `2025.06` for monthly migrations
* Year-based: `2025`

Example path:

```
app/schema/migrations/db/0.1.0/v20250604125127_create_users_table.py
```

---

## Creating a Database Migration

To generate a new database migration file:

```bash
# uv run arkalos make:mig {version}/{table_name}

uv run arkalos make:mig 0.1.0/users
```

This creates a migration template at `app/schema/migrations/db/...`:

```python
from arkalos import DB
from arkalos.schema.database_migration import DatabaseMigration

class Migration(DatabaseMigration):

    def up(self):
        with DB().createTable('users') as table:
            table.col('id').id()
            table.col('created_at').datetime().notNull().defaultNow()
            table.col('updated_at').datetime().notNull().defaultNow()

    def down(self):
        DB().dropTable('users')
```

If you create migrations manually, name the file starting with `v` and use underscores: `_`.



## Creating a Data Warehouse Migration

To create a data warehouse migration:

```bash
uv run arkalos make:mig:dwh 0.1.0/users
```

This generates a file in:

```
app/schema/migrations/dwh/...
```

Example migration:

```python
from arkalos import DWH
from arkalos.schema.data_warehouse_migration import DataWarehouseMigration

class Migration(DataWarehouseMigration):

    def up(self):
        with DWH().clean().createTable('users') as table:
            table.col('id').id()
            table.col('created_at').datetime().notNull().defaultNow()
            table.col('updated_at').datetime().notNull().defaultNow()

    def down(self):
        DWH().clean().dropTable('users')
```



## Data Warehouse Layers

Most data warehouses follow a **three-layer architecture**:

* `raw` (bronze): lightly structured ingestion layer
* `clean` (silver): normalized, validated data
* `BI` (gold): business-ready reporting tables

You can target these layers using the appropriate facade:

```python
DWH().raw().createTable('...')
DWH().clean().createTable('...')
DWH().BI().createTable('...')
```



## Running and Rolling Back Migrations

Run all database migrations:

```bash
uv run arkalos migrate
```

Run only a specific version:

```bash
uv run arkalos migrate 0.1.0
```

Rollback the last database migration:

```bash
uv run arkalos rollback
```

Run all data warehouse migrations:

```bash
uv run arkalos migrate:dwh
```

Rollback data warehouse migrations:

```bash
uv run arkalos rollback:dwh
```



## Alter Tables with SQLGlot and Ibis

To alter a table instead of creating it, use `alterTable()`:

```python
with DWH().clean().alterTable('users') as table:
    table.col('new_col').text()
```



## Table Syntax and Column Types

Start by creating or altering a table with:

```python
with DWH().clean().createTable('users') as table:
    table.col('id').id()
```

### Column Types

**Integer:**

* `.id()` – Primary key, auto-increment bigint
* `.integer()` / `.tinyInt()` / `.smallInt()` / `.bigInt()`
* `.uInteger()` / `.uTinyInt()` / `.uSmallInt()` / `.uBigInt()` – Unsigned

**Decimal:**

* `.decimal(total=8, places=2)`

**Text:**

* `.string(length=255)` – VARCHAR
* `.text()` – TEXT

**Boolean:**

* `.boolean()`

**Date & Time:**

* `.datetime()`
* `.date()`

### Column Modifiers

* `.notNull()` – NOT NULL
* `.nullable()` – Allow NULLs (removes NOT NULL on ALTER)
* `.default(value)` – Static default
* `.defaultNow()` – Use current timestamp
* `.defaultFunction(func_name, param)` – Custom default function

### Indexes

```python
with DWH().clean().alterTable('users') as table:
    table.col('email').string().notNull()
    table.indexUnique('email')
```

* `.indexUnique(column_name)` – Adds a UNIQUE INDEX

### Foreign Keys

For relational databases:

```python
with DB().alterTable('posts') as table:
    table.col('user_id').uBigInt().notNull()
    table.foreignKey('user_id', ref_table='users', on_ref_col='id')
```

For data warehouses:

```python
table.foreignKey(
    'user_id',
    ref_table='users',
    on_ref_col='id',
    ref_table_group=DWH().clean().layerName()
)
```



## Python Query Builder (with SQLGlot ORM-style syntax)

Use Arkalos' built-in query builder to interact with your DB or DWH from notebooks, scripts, or your FastAPI app.

```python
from arkalos import DB, DWH
from pydantic import BaseModel
import polars as pl
import datetime

class UserType(BaseModel):
    id: int
    name: str
    email: str
    is_admin: bool = False
    created_at: datetime.datetime
    updated_at: datetime.datetime

class InsertUserType(BaseModel):
    name: str
    email: str
    is_admin: bool = False

db = DB()
dwh = DWH()

# Connect before running queries
dwh.connect()

# Your queries here

# Disconnect after
dwh.disconnect()
```

### Raw SQL

```python
dwh.executeSql("INSERT INTO users ...")
df = dwh.executeSql("SELECT ...", select=True)  # Returns Polars DataFrame
```

### Ibis Tables

```python
df = dwh.raw().selectAll('airtable__properties')
table = dwh.raw().table('notion__projects')  # ibis Table
```

See [Ibis Tutorials](https://ibis-project.org/tutorials/basics) for more.

### Insert Records

```python
db.insert('users', user)

# or get inserted row
user = db.insertReturning('users', user)
# or get inserted row as a full model type
db.insertReturning('users', user, UserType)

# To insert multiple records:
db.insertMultiple('users', users)  # DataFrame or list of dicts
```

### Update

```python
user.is_admin = True
res = db.update('users', user, 'email', user.email)
```

### Delete

```python
dwh.clean().delete('users', 'email', 'test@example.com')
```

### Explore Schema

```python
dwh.printTablesAndColumns('raw')
```

