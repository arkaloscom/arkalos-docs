
# Python Project Installation

> [!NOTE]
> Arkalos is in Beta, where the code may change or break without notice. Not all documentation and modules are complete. Use it only for experimentation, study, and practice, not for production use. [Follow us and join the community](/contact) to stay updated.


## Requirements

* Git
* Python > 3.13
* Python `uv` package manager > 0.5.29
* Ollama - for downloading, running, and building LLMs locally.
* IDE - like VS Code
* Just basic coding skills. You don't have to know math, stats, ML/AI to use core Arkalos features.


## For Windows Users

If you're on Windows, we recommend using Windows Subsystem for Linux (WSL).

Check out Microsoft's [installation guide](https://learn.microsoft.com/en-us/windows/wsl/install).


## Git

You likely have Git installed already.

If not, follow GitHub's [Installing Git guide](https://github.com/git-guides/install-git).

To verify installation:

```bash
git --version
```


## UV Package Manager

`uv` is a modern, super-fast Python package and project manager.

If you've used NodeJS (npm) or PHP (Composer), it's similar.

- `pyproject.toml` is like `package.json`.
- Use commands like `uv add <package>` or `uv sync` to manage dependencies.

To install `uv`:

=== "Mac, WSL, and Linux"

    ```bash
    curl -LsSf https://astral.sh/uv/install.sh | sh
    ```

=== "Windows"

    ```bash
    powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
    ```

For more details, see the [uv documentation](https://docs.astral.sh/uv/getting-started/installation/).

To verify installation:

```bash
uv -V
```


## Python

Download and install the latest Python version from the [official site](https://www.python.org/downloads/).

Alternatively, use `uv` to install Python:

```bash
uv python install
```

To verify installation:

```bash
python -V
```


## Installing Ollama and Open-Source AI Models

Ollama is a sleek, user-friendly app, CLI, framework, and repository for managing open-source LLMs locally.

Download and install [Ollama](https://ollama.com/download).

To verify installation:

```bash
ollama -v
```

For more details, CLI commands, and RAM requirements, visit the [Ollama GitHub repository](https://github.com/ollama/ollama).

Once installed, open a new terminal and start the Ollama server:

```bash
ollama serve
```

Now, you can download various open-source LLMs like DeepSeek, Llama, Mistral, and Gemma.

Open a new terminal and download your preferred model. Smaller models (~5GB) will take some time.

You'll need at least 8GB of RAM for 7B models.

Arkalos includes a basic AI Agent — a terminal chatbot to interact with your data warehouse.

We recommend downloading the [qwen-2.5-coder](https://ollama.com/library/qwen2.5-coder) model (7B by default) while continuing with this guide:

```bash
ollama pull qwen2.5-coder
```


## Recommended VS Code Settings and Extensions for Python Data & AI Projects

We suggest using VS Code as your IDE because:

* It supports Jupyter Notebooks and other extensions.
* It includes a copilot.
* For Windows and WSL users, it simplifies working inside WSL.

Download and install [VS Code](https://code.visualstudio.com/download).

### Install Extensions:

From the Extensions side tab, search for and install:

**Official Microsoft Extensions:**

* Python
* Python Debugger
* Pylance
* Data Wrangler
* Mypy Type Checker
* isort
* Jupyter (and extension packs)
* WSL (for Windows + WSL users)

**Other Recommended Extensions:**

* Project Manager (by Alessandro Fragnani)
* DotENV (by mikestead)
* vscode-pdf (by tomoki1207)
* SQLite Viewer (by Florian Klampfer)
* Excel Viewer (by MESCIUS)
* Material Icon Theme (for a nicer look) (by Philipp Kief)


## Create a New Arkalos Project

You're all set! Now, you can create a [new Arkalos project](/docs/new-project).
