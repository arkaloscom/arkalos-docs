# Writing Your First Arkalos Code and Notebook

This guide focuses on using VS Code with the Jupyter extension, as [explained previously](/docs/installation/#install-extensions).

You may need to install the development dependency:

```bash
uv add ipykernel --dev
```


## Creating a New `.ipynb` File

Jupyter Notebooks are a great place to start exploring data, designing new code, experimenting, or prototyping.

Notebooks contain cells where each cell functions like an independent script. You can run all cells or specific ones manually.

When cells are run, all imported modules and declared variables are stored in memory and available in other cells. If a specific cell is skipped, its code won't be available in memory.

Each cell has a single output displayed right after it. You can debug the code by printing variable values or, when analyzing data, display data frames (tables) first and generate visual charts in the next cell.

Create a new `notebooks/my_notebook.ipynb` file inside the `notebooks/` folder.


## First Cell

In the middle of the notebook, click on "+ Code" to add a new cell.

In the top right corner of VS Code, select "Select Kernel" and choose the option that looks like:

`.venv (Python 3.13.1) .venv/bin/python`.

Add this code to your first cell:

```python title="notebooks/my_notebook.ipynb (Cell 1)"
from arkalos import config, var_dump

point = {'x': 5, 'y': 10}

var_dump(point)
```


## Running and Resetting

Click "Run All" at the top to execute all cells from the beginning.

You can also run each cell individually by clicking the play triangle icon on the left of the cell.

If something doesn't work properly, or if you modify custom modules in the `app/` folder after running a cell, click "Restart" to restart the Jupyter kernel and clear cache and memory.


## Another Cell

You should see some output after running the first cell.

Hover your mouse in the middle at the end of the output, and the "+ Code" button will appear. Click it to create the next cell.

Alternatively, you can click the "+ Code" button in the top horizontal menu.

Add this code to the new cell and run it:

```python title="notebooks/my_notebook.ipynb (Cell 2)"
config('app.name')
```

Unlike scripts, notebook cells automatically print the last variable or operation, so you don't need to use the `print()` function every time.

You should see the name of your app as the output of this cell.


## Create Custom Modules

Remember from the [Getting Started Guide](/docs/structure/#code-to-run-notebooks-and-scripts) the difference between **Code to Run** and **Code to Reuse**.

While your initial thoughts about reusable functions, classes, configurations, or modules can start in a notebook or script, you should eventually move all reusable code into the `app/` directory.

Let's create the `app/utils/my_utils.py` file, which will also act as a Python module. Add your first function there:

```python title="app/utils/my_utils.py"
from arkalos import config

def greet(greeting: str) -> None:
    print(greeting + ', ' + config('app.name'))
```

Now, consume your first reusable code and module in the notebook by creating a new cell and running it:

```python title="notebooks/my_notebook.ipynb (Cell 3)"
import app.utils.my_utils as my_utils

my_utils.greet('Hello')
```


## Analyze and Visualize Data

Let's analyze and visualize some data from your Airtable or Notion database.

```python title="notebooks/my_notebook.ipynb (Cell 4)"
import pandas as pd
from arkalos.data.extractors.notion_extractor import NotionExtractor
# from arkalos.data.extractors.airtable_extractor import AirtableExtractor

extractor = NotionExtractor()
# extractor = AirtableExtractor()

# Provide a table name from the .env file
data = extractor.fetchAllData('Tasks')  

df = pd.DataFrame(data)

df
```

Run this cell, and you should see a table.

Finally, let's create a new cell to generate a pie chart.

Assume you have a Tasks table with a "Status" column.

This code will display a pie chart of task statuses by percentage:

```python title="notebooks/my_notebook.ipynb (Cell 5)"
# "Status" is a column name. To access it use df.Status or df['Status']
df.Status.value_counts().plot(kind='pie', autopct='%1.0f%%', startangle=90)
```

Now update your Notion or Airtable by adding a new task and changing a few statuses. Then re-run all the cells and you will see an updated chart that reflects your data real-time.


## From Notebook to Scripts

Now that you have first experience writing code using Arkalos, let's continue and create our first [script](/docs/scripts) and run it from the terminal.

