
# Python Scripts

Scripts are simple programs that run tasks automatically. Unlike Notebooks, they don't need a UI like Jupyter and can be run from the terminal, a server, or even as an API. They're great for automating workflows, launching a microserver or an agent.




## Why Use Scripts?

Your reusable code lives in the `app/` folder, but it won't do anything by itself. Scripts are how you make that code run — whether it's a data pipeline, analysis, automation, AI agent or an app.

## Types of Scripts

1. **One-Time Scripts**: These scripts run once and stop. Think of tasks like analyzing data or running an ETL process to load data into a warehouse.

2. **Continuous Scripts**: These run non-stop, like a web server or a chatbot that stays active in your terminal. They keep running until you stop them manually.




## Your First Script

First, create a utility module inside `app/utils/` folder:

```python title="app/utils/my_utils.py"
from arkalos import config

def greet(greeting: str) -> None:
    print(f"{greeting}, {config('app.name')}")
```

Now, create your first script inside `scripts/cli/` folder.

```python title="scripts/cli/my_script.py"
import app.utils.my_utils as my_utils

GREETING = "Hello"

print('Running my script...')
my_utils.greet(GREETING)
print('Done. Bye!')
```




## Running Scripts

To run your script, use the terminal command `uv run <path>`:

```bash
uv run scripts/cli/my_script.py
```

While typing the path, use Tab for autocompletion.




## Adding Arguments to Scripts

Scripts can take inputs, just like functions. Update your script to accept a greeting message:

```python title="scripts/cli/my_script.py"
import argparse
import app.utils.my_utils as my_utils

parser = argparse.ArgumentParser(description="My Greeting Script")
parser.add_argument('greeting', metavar='<greeting>', help='A greeting message')
args = parser.parse_args()

print('Running my script...')
my_utils.greet(args.greeting)
print('Done. Bye!')
```

Run it with an argument (add quotes if there are spaces):

```bash
uv run scripts/cli/my_script.py "Hello World"
```

For help, type:

```bash
uv run scripts/cli/my_script.py -h
```




## Script Example: Import Data into a Warehouse

Let's pull data from your Airtable or Notion into a local data warehouse.

Create a new script:

```python title="scripts/etl/sync_airtable_dwh.py"
from arkalos.data.extractors.airtable_extractor import AirtableExtractor
# or for Notion
# from arkalos.data.extractors.notion_extractor import NotionExtractor
from arkalos.workflows.etl_workflow import ETLWorkflow

wf = ETLWorkflow(AirtableExtractor)
wf.run(drop_tables=True)
```

Run it:

```bash
uv run scripts/etl/sync_airtable_dwh.py
```

You'll find your data in `data/dwh/dwh.db`. Now you can analyze data offline!




## Script Example: Talk to Your Data

Now, let's chat with your data using AI.

First, make sure the Ollama server is running. In VS Code in the terminal section at the bottom, you can press "+" to open a new terminal tab and run ollama server there.

```bash
ollama serve
```



Create a new script:

```python title="scripts/ai/dwh_agent.py"
from arkalos.ai.agents import DWHAgent

agent = DWHAgent()
agent.run()
```

Run the agent:

```bash
uv run scripts/ai/dwh_agent.py
```

Now you can ask questions like:

* What tables do we have?
* Show me all tasks.
* Show rooms with a balcony.

Type `exit` to stop the agent.




## Stopping Scripts

To stop a long-running script, press `Ctrl + D` or `Ctrl + C` in your terminal.




## Next Steps

Now that you've learned about the **Code to Run** (notebooks and scripts), check out the next [Code to Reuse (App & Modules)](/docs/app) guide to organize your reusable code.

