
# Python Project Configuration and Environment Setup

Arkalos uses environment variables and configuration files to manage settings across your entire app and any workflow, script or notebook.

You can get env variables via `env('KEY')` function.

Or config settings via `config('config_file.key')`.



## Environment Variables (`.env`)

When you start an Arkalos project, you'll notice two environment files:

1. **.env.example** - A template file with sample environment variables.
2. **.env** - Your actual configuration file, which is **gitignored** to prevent sensitive data from being committed to version control.

Whenever you add a new custom env variable, add an empty or default value to the `.env.example` as well so your team would know about them.



### Why Use Environment Variables?

Environment variables allow you to:

- **Secure sensitive data** like API keys, database credentials, and tokens.
- **Easily switch between environments**, such as local development and production servers, by changing the settings in your `.env` file.
- **Collaborate with teams** without sharing sensitive credentials — team members can use the `.env.example` file as a reference.

### Example `.env` File

```ini
# - MAIN / APP -
APP_NAME=My App
APP_ENV=local
APP_KEY=generated automatically

# - DATA WAREHOUSE -
DWH_ENGINE=SQLite
DWH_SCHEMA_PATH=data/dwh/schema.sql
DWH_SQLITE_PATH=data/dwh/dwh.db
# DWH_HOST=127.0.0.1
# DWH_PORT=3306
# DWH_DATABASE=warehouse
# DWH_USERNAME=root
# DWH_PASSWORD=

# - LLM (Models available on Ollama) -
LLM=qwen2.5-coder
# LLM=deepseek-r1:1.5b
# LLM=deepseek-r1:7b

# - DATA SOURCES (CONNECTORS / EXTRACTORS) -
AIRTABLE_API_KEY=
AIRTABLE_BASE_ID=
AIRTABLE_TABLES='[
    {"id": "", "name": ""},
]'

GOOGLE_SERVICE_ACCOUNT_KEY_PATH=data/keys/gdrive.json
GOOGLE_SPREADSHEETS=

NOTION_API_SECRET=
NOTION_DATABASES=

MONDAY_API_KEY=
MONDAY_TABLES=

HUBSPOT_API_KEY=

# - LLM & AI API KEYS -
DEEPSEEK_API_KEY=

OPENAI_API_KEY=
```

> [!NOTE]
> Some environment variables contain JSON strings. Make sure:
>
> - The JSON is valid.
> - Use **single quotes** around the entire string and **double quotes** inside the JSON.
> - Pay attention to commas: no trailing commas after the last item.






## Configuration Files

Arkalos uses Python-based configuration files stored in the `config/` folder. Unlike traditional text-based config files (like `.json` or `.yaml`), Python config files allow you to:

- Use functions and logic inside the configuration.
- Dynamically load environment variables using the `env()` function.

### Available Config Files

1. **config/app.py** - General app settings.
2. **config/data_sources.py** - Configurations for data connectors (Airtable, Google Sheets, etc.).
3. **config/data_warehouse.py** - Data warehouse settings (SQLite, PostgreSQL, etc.).

### Example Configurations

#### `config/app.py`

```python
from arkalos import env

config = {
    'name': env('APP_NAME', 'Arkalos App'),
    'env': env('APP_ENV', 'production'),
    'llm': env('LLM', 'qwen2.5-coder'),
    'debug': env('APP_DEBUG', False),
}
```

#### `config/data_sources.py`

```python
from arkalos import env

config = {
    'airtable': {
        'enabled': False,
        'api_key': env('AIRTABLE_API_KEY'),
        'base_id': env('AIRTABLE_BASE_ID'),
        'tables': env('AIRTABLE_TABLES'),
    },
    'google': {
        'enabled': False,
        'service_account_key_path': env('GOOGLE_SERVICE_ACCOUNT_KEY_PATH'),
        'spreadsheets': env('GOOGLE_SPREADSHEETS'),
        'id_col': 'id',
        'updated_at_col': 'updated_at'
    },
    'hubspot': {
        'enabled': False,
        'api_key': env('HUBSPOT_API_KEY'),
        'objects': ['contacts', 'companies', 'deals'],
    },
    'monday': {
        'enabled': False,
        'api_key': env('MONDAY_API_KEY'),
        'databases': env('MONDAY_DATABASES'),
    },
    'notion': {
        'enabled': False,
        'api_secret': env('NOTION_API_SECRET'),
        'databases': env('NOTION_DATABASES'),
    },
}
```

#### `config/data_warehouse.py`

```python
from arkalos import env

config = {
    'engine': env('DWH_ENGINE', 'sqlite'),
    'schema_path': env('DWH_SCHEMA_PATH', 'data/dwh/dwh.sql'),
    'sync_frequency': '1h',

    # SQLite
    'path': env('DWH_SQLITE_PATH', 'data/dwh.db'),

    # PostgreSQL, etc.
    'host': env('DWH_HOST', '127.0.0.1'),
    'port': env('DWH_PORT', '3306'),
    'database': env('DWH_DATABASE', 'warehouse'),
    'username': env('DWH_USERNAME', 'root'),
    'password': env('DWH_PASSWORD', ''),

    'engines': {
        'sqlite': {},
        # 'postgresql': {},
        # 'bigquery': {},
        # 'snowflake': {}
    }
}
```


## Creating a New Configuration File

To add a new config file:

1. **Create a new Python file** in the `config/` folder (e.g., `config/new_config.py`).
2. Add the following structure:

```python
from arkalos import env

config = {
    'key_name': env('ENV_VARIABLE', 'default_value'),
    # Add more configurations here
}
```

Now, your new configuration is ready to be used in your project.

You can access it via `config('new_config.key_name')`



## Default Language Model (LLM)

Choose which language model your app will use by default by specifying its name in the `.env` file:

```ini
LLM=qwen2.5-coder
```

If you followed previous installation guide, the `qwen2.5-coder` model from Ollama should already be set up on your system. This lightweight model works smoothly on a typical laptop or server with at least 8 GB of RAM. It’s good enough for basic chatting or generating code snippets.

If your machine is more powerful (especially with a high-end GPU), feel free to switch to a larger model by simply changing the model name in your configuration. And when moving to production, we recommend using an enterprise server or cloud services with sufficient memory and GPU.



## Data Sources, Extractors and Connectors

Arkalos currently supports these providers out of the box:

* Notion (databases only)
* Airtable (tables)

Google Drive, Monday.com, HubSpot and others are comming soon.

For other data sources, You can create your own implementation by using the `DataExtractor` contract.




### Notion

Create a new Integration and API Secret by following the [Notion guide](https://developers.notion.com/docs/create-a-notion-integration#getting-started).

You must be an Owner or Admin of the workspace. Select your workspace and select type: Internal. Generate and copy a new secret into `.env` file.

```ini title=".env (this is an example, not a valid real secret)"
NOTION_API_SECRET=ntn_3465436gigtw87wq4yNwugKGrhwefeiw7gtwogcyt
```

Now open a Notion database as a separate page that you wish to sync into your warehouse. For practice, you can create a new workspace and use the default Project and Tasks template. Copy the ID for each database you wish to sync. You can find the ID in the URL right after the domain:

notion.so/**``158a035340380c5a990dgfgwejfwef``**?v=35g3k3tk4333yh3hy34996b

Update the `.env` file and enter a new JSON row. Make sure there is no comma after the last item. Copy the ID and provide a name for each database. It can be any name you wish. Arkalos Data Warehouse will create a new table for each data source as `<source_name>__<table_name>`, e.g., `Notion__Tasks`.

```ini title=".env"
NOTION_API_SECRET=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
NOTION_DATABASES='[
    {"id": "158a035340380c5a990dgfgwejfwef", "name": "Tasks"},
    {"id": "159a035340380c5a990dgfgwejfwef", "name": "Projects"}
]'
```

Finally, enable your integration on EACH database page.

In the top right corner, from the "..." menu, select Connections and then search for and enable your new integration.


### Airtable

In Airtable, under your user menu in the top right corner, open [Builder Hub](https://airtable.com/create/tokens).

Create a new Personal Access Token. Type any name for your integration. Select these two scopes:

* `data.records:read`
* `schema.bases:read`

Under Access, select your Airtable workspace and save the token into `.env`. For practice, you may use Airtable AI App builder to generate a new app and tables with some data.

```ini title=".env"
AIRTABLE_API_KEY=patHglggwgel.93IGLi5feih64da9c37853256gi75e4072d24353452g5ge
```

Open your workspace and copy the Base ID as well. It starts with "app..." and you can find it right after the domain:

airtable.com/**``appgJGkGY65KYrjf6fK``**/tblhj3hk796wolwe7/viw5tuGutk8t6l

```ini title=".env"
AIRTABLE_BASE_ID=appgJGkGY65KYrjf6fK
```

Finally, update the `.env` file and enter a new JSON row. Make sure there is no comma after the last item. Copy the ID and provide a name for each table from the URL (starts with "tbl..."). It can be any name you wish. Arkalos Data Warehouse will create a new table for each data source as `<source_name>__<table_name>`, e.g., `Airtable__Tasks`.

airtable.com/appgJGkGY65KYrjf6fK/**``tblhj3hk796wolwe7``**/viw5tuGutk8t6l

```ini title=".env"
AIRTABLE_TABLES='[
    {"id": "tblhj3hk796wolwe7", "name": "Tasks"},
    {"id": "tblwj3hk796wolwe7", "name": "Projects"}
]'
```



## Data Warehouse

**Universal settings:**

```ini title=".env"
DWH_SCHEMA_PATH=data/dwh/schema.sql
```

- **`DWH_SCHEMA_PATH`**: Arkalos automatically generates and updates this file. It contains the SQL structure of your data. For example, when a new data source is synced into the warehouse, Arkalos **infers the schema** (data structure) automatically by analyzing the first rows of the incoming data.


**Engine-specific settings:**

Currently, only the SQLite engine is supported out of the box.

```ini title=".env"
DWH_ENGINE=SQLite
DWH_SQLITE_PATH=data/dwh/dwh.db
```

You can create your own implementation by using the `DataWarehouse` contract.

You can access the data warehouse from any part of your notebook, script, or application:

```python
from arkalos import dwh

dwh().connect()
```


## Let's Write Our First Arkalos Code!

That's it! You have completed the Getting Started guide and are ready to [write some code](/docs/notebooks)!

