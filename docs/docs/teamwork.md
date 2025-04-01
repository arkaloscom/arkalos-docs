# Working as a Team

Frequently commit your changes with git and push them to a GitHub or other repository so your teammates, school, or organization can access your code.

Even if you work alone, using Git helps you share your code between multiple devices. For example, you can work or study at home or in a café and then continue from your class, library, or office the next day.

## Where to Start Writing App Code?

Check the [folder structure](/docs/structure) guide to understand the default Arkalos project layout. However, you are not required to follow it strictly. Feel free to remove unnecessary folders and adjust the structure to fit your project.

For example:

- If your project covers multiple domains or departments, you can create an `app/domains/` folder.
- If you are building a native desktop app, you might add an `app/ui/` folder.
- If developing a game engine, you can use the default `app/core/` folder or create `app/engine/`.

## STEM Students, Teachers, Schools, and Universities

If you are a STEM student or self-learning math, statistics, or ML, you can write your implementations in the `app/algorithms/` folder, separate from notebooks.

For schools and universities, a single Arkalos repository can host all class materials, including default modules and Notebook templates. You may organize code by a topic or a year using subfolders, such as `notebooks/ml101/` and `notebooks/ml102/`.

## Data Analysis, ETL, Migration, Visualization, and Warehousing

For working with data (custom types, transformation, visualization, extraction, or analysis), use the `app/data/` folder. This is different from the `data/` folder, which stores downloaded files and datasets.

If you are an indepedent data analyst, researcher, freelancer, consultant, or an agency, you do not have to create a new Arkalos project every time and can simply create a folder for each client while sharing the same app code, e.g. `notebooks/acme_corp/` for one client, and `notebooks/nova_solutions/` - for another.

## Training a Model or Building an AI Agent

If you're developing an AI agent or training a model, start in the `app/ai/` folder.

## Automating Business Processes and Workflows

To automate processes or create workflows, use the `app/workflows/` folder.

## Scientific Method, Workflows and Experiments

If you are a Master's or PhD student, data scientist, business analyst or a product manager designing experiments (e.g., A/B testing or hypothesis testing), use the `app/workflows/experiments/` subfolder.

## Extending Arkalos and Writing Core Helpers and Modules

To modify Arkalos core functions like `config()`, create implementations in `app/core/`.

For general utility functions, such as custom string handling, place them in `app/utils/`.

## Robotics, Hardware, AVs, and More

If working with robotics or hardware, start in the `app/hardware/` folder.



## What Next

Now that we know about the app code, modules and working as a team, let's continue to [Workflows](/docs/workflows).
