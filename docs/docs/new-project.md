# Creating a New Arkalos Project

Ensure all the [requirements](/docs/installation) are met.


## Starting a New Arkalos Project

```bash
# Create a new folder and Python project.
uv init <project_name>

# Navigate to the new folder
cd <project_name>

# Add and install all dependencies (this may take a few minutes)
uv add arkalos

# Set up the Arkalos starter project
uv run arkalos init
```

This will install Arkalos along with dependencies like NumPy, SciPy, Polars, Pandas for data frames, matplotlib, Ollama, and more.

Your project folder structure will be automatically set up.

That's it — you're ready to start coding!


## Using VS Code

1. Make sure you're in the project folder in the terminal.
2. Run `code .` to open a new VS Code window in the current directory.
3. Open the [Project Manager extension](/docs/installation/#install-extensions) from the side tab.
4. In the side menu, hover under Favorites and click "Save Project."
5. Next time, easily open this project by double-clicking it in the Project Manager. To work on multiple projects, hover over the project name and click the "Open in New Window" icon on the right.


## Working on an Existing Project

```bash
git pull <your project repo>

uv sync
```


## Folder Structure

Learn more about the Arkalos [directory structure](/docs/structure) and where to organize your files.

