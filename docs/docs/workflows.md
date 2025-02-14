# Workflows

Workflows are structured sequences of steps or processes that automate tasks. They are something between a script and a package.

Often, when you start working with data pipelines or automation, you begin by writing scripts and notebooks. However, you may find yourself copying and adapting code for different use cases repeatedly. Workflows help by organizing these repetitive tasks into reusable components.

A workflow acts as a controller that brings together multiple modules and data, executing them in a specific order.

## Creating a Workflow

To create a new workflow, add a new file and class inside the `app/workflows/...` directory. Implement the `Workflow` contract (interface), which requires a `run()` method.

Let's say we need a simple workflow with two steps:

```python title="app/workflows/processes/my_workflow.py"
from arkalos.workflows.workflow import Workflow

class MyWorkflow(Workflow):
    
    def step1(self):
        print("Running step 1 of My Process...")
    
    def step2(self):
        print("Running step 2 of My Process...")
    
    def run(self):
        self.step1()
        self.step2()
```

Now, this workflow can be reused across different scripts and notebooks:

```python title="scripts/my_workflow.py"
from app.workflows.processes.my_workflow import MyWorkflow

wf = MyWorkflow()
wf.run()
```

Run the script using:

```bash
uv run scripts/my_workflow.py
```

## ETLWorkflow

ETL stands for Extract, Transform, and Load — a common pattern in data pipelines. Arkalos includes an `ETLWorkflow` that simplifies data extraction and loading into a configured data warehouse.

To use it, provide an extractor class as the first argument:

```python title="scripts/etl/sync_airtable_dwh.py"
from arkalos.data.extractors.airtable_extractor import AirtableExtractor
from arkalos.workflows.etl_workflow import ETLWorkflow

wf = ETLWorkflow(AirtableExtractor)
wf.run(drop_tables=True)
```

The `run()` method accepts parameters such as `drop_tables=True`, which ensures data is refreshed on every run.

> [!NOTE]
> Currently, we recommend dropping tables in your warehouse since Arkalos is in development. This ensures data is always in sync but is not ideal for handling large datasets.

Optionally, you can specify a data warehouse class as a second argument:

```python title="scripts/etl/my_custom_etl.py"
from arkalos.data.extractors.airtable_extractor import AirtableExtractor
from arkalos.data.warehouse.sqlite.sqlite_warehouse import SQLiteWarehouse

wf = ETLWorkflow(
    AirtableExtractor,
    SQLiteWarehouse
)
wf.run(drop_tables=True)
```

You can also create your own extractor and warehouse implementation. See the **Working with Data** guide for more details.

## What’s Next?

Now that you understand workflows, continue to the [Utils](/docs/utils) section to learn about utility functions and helpers.

