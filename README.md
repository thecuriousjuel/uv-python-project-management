# Quick Guide to Astral `uv` Commands

`uv` is an extremely fast, single-tool replacement for your entire Python packaging and environment workflow. It replaces `pip`, `pip-tools` (`pip-compile`/`pip-sync`), `virtualenv`, `pipx`, `pyenv`/`fnm`, and `poetry`/`hatch` in a single executable.

Here is a quick reference guide to the core commands of `uv` with practical examples.

## Install uv from Astral
* **Documentation:** https://docs.astral.sh/uv/
* **Installation Guide:** https://docs.astral.sh/uv/getting-started/installation/

---

## How we create python project using venv, pip and requirements.txt

```bash
python -m venv .venv
source .venv/bin/activate
pip install requests
pip freeze > requirements.txt
pip uninstall requests
deactivate
```

---
## 1. Project & Package Management (Modern Workflow)
These commands are used when managing a project with a `pyproject.toml` file (similar to Poetry or Hatch).

### `uv init`
Initializes a new Python project or standalone script.
* **Example (New App in New Directory):**
  ```bash
  uv init my-app
  ```
  *Creates a directory with a standard `pyproject.toml`, a `.python-version` file, and a boilerplate `main.py`.*
* **Example (Initialize in Current Directory):**
  ```bash
  uv init
  ```
  *Running `uv init` without a project name creates all the necessary project files (like `pyproject.toml`, `.python-version`, and `hello.py` or `main.py`) directly inside the current folder.*
* **Example (Standalone Script with Metadata):**
  ```bash
  uv init --script analyze.py
  ```
  *Creates a single script file containing inline dependency metadata (PEP 723).*

### Create and execute the standalone script
* **Initialize script:**
  ```bash
  uv init --script main.py
  ```
* **Add dependencies to script:**
  ```bash
  uv add --script main.py requests
  ```
* **Run standalone script:**
  ```bash
  uv run main.py
  ```

### `uv add`
Adds packages to your project's dependencies and automatically updates the lockfile (`uv.lock`) and virtual environment (`.venv`).
* **Example (Standard Dependency):**
  ```bash
  uv add requests
  ```
* **Example (Dev Dependency):**
  ```bash
  uv add pytest black --dev
  ```
* **Example (Git/URL/Path Dependency):**
  ```bash
  uv add git+https://github.com/encode/httpx
  ```
* **Example (From Requirements File):**
  ```bash
  uv add -r requirements.txt
  ```
  *Adds all dependencies listed in a requirements file to the project.*

### `uv remove`
Removes packages from your project's dependencies and cleans up your virtual environment.
* **Example:**
  ```bash
  uv remove requests
  ```

### `uv run`
Runs a command, module, or script in the context of a virtual environment. If the script has inline dependencies, `uv` will download them to an ephemeral environment and run it on the fly.
* **Example (Run Project Script):**
  ```bash
  uv run main.py
  ```
* **Example (Check Python Version):**
  ```bash
  uv run python --version
  ```


### `uv sync`
Synchronizes the local virtual environment (`.venv`) to match the exact state defined in `uv.lock`.
* **Example:**
  ```bash
  uv sync
  ```

* **Example (Exclude Development Dependencies):**
  ```bash
  uv sync --no-dev
  ```
  *Synchronizes the local virtual environment, excluding development dependencies.*

Install dependencies from the lock file into the the local virtual environment (`.venv`)
* **Example:**
  ```bash
  uv sync --frozen
  ```

* **Example (Sync with Specific Python Version):**
  ```bash
  uv sync --python 3.10
  ```

* **Example (Sync with Active Virtual Environment):**
  ```bash
  uv sync --active
  ```
  *Synchronizes the project dependencies into the currently active virtual environment.*
 

### `uv lock`
Resolves the project dependencies and updates the `uv.lock` lockfile without modifying the virtual environment.
* **Example:**
  ```bash
  uv lock
  ```

### `uv tree`
Displays the project's complete dependency tree in a clean, hierarchical format.
* **Example:**
  ```bash
  uv tree
  ```



### `uv format`
Formats Python code in your project using Ruff. By default, it formats all files in the project. If you want to format a specific file or directory, you must pass it after a double dash `--`.
* **Examples:**
  ```bash
  uv format                        # Format all Python files in the project
  uv format --check                # Check formatting without writing changes
  uv format --diff                 # Show formatting diff without writing changes
  uv format -- main.py             # Format only main.py (use '--' before filenames)
  ```





## 2. Pip-Compatible Interface (`uv pip`)
If you want to keep using standard `requirements.txt` files (instead of the modern project-based `pyproject.toml` workflow), `uv pip` provides an extremely fast, drop-in replacement for traditional `pip` commands.

### uv with venv and pip
```bash
uv venv
uv pip list
uv pip install seaborn
uv pip list
uv run main.py
uv pip freeze > requirements.txt
uv pip install -r requirements.txt
```

### Migration to Modern Workflow (from requirements.txt)
```bash
uv init
uv add -r requirements.txt
rm -rf requirements.txt
```

### `uv pip install`
Installs packages into the active virtual environment at lightning speed.
* **Example (Single Package):**
  ```bash
  uv pip install requests
  ```
* **Example (From Requirements File):**
  ```bash
  uv pip install -r requirements.txt
  ```

---

## 3. Utility & Cache Management

### `uv venv`
Creates a new virtual environment at lightning speed.
* **Example (Default environment):**
  ```bash
  uv venv
  ```
  *Creates a `.venv` directory using the default Python version.*
* **Example (With specific Python version):**
  ```bash
  uv venv --python 3.10
  ```

### `uv cache clean`
Removes all cached package wheels and metadata, freeing up disk space.
* **Example:**
  ```bash
  uv cache clean
  ```

### `uv version`
Displays the name and version of the current project if run inside a project directory, or the version of the `uv` tool itself if run outside a project.
* **Example:**
  ```bash
  uv version
  ```

---

## 4. Python Version Management (`uv python`)
Replaces tools like `pyenv` or `conda` for installing and managing Python installations.

### `uv python install`
Downloads and installs specific Python runtimes on your machine.
* **Example (Install Specific Python Version):**
  ```bash
  uv python install 3.10
  ```
* **Example (Install Latest Python):**
  ```bash
  uv python install 3.12
  ```
* **Example (Install Multiple Pythons):**
  ```bash
  uv python install 3.10 3.11 3.12
  ```

### `uv python uninstall`
Uninstalls specific Python runtimes managed by `uv`.
* **Example (Uninstall Specific Version):**
  ```bash
  uv python uninstall <version>
  ```
* **Example (Uninstall Outdated Versions):**
  ```bash
  uv python uninstall --outdated
  ```
* **Example (Uninstall All Managed Versions):**
  ```bash
  uv python uninstall --all
  ```

### `uv python list`
Lists all Python versions available on your system, as well as those available for download.
* **Example:**
  ```bash
  uv python list
  ```

### `uv python pin`
Pins a project to a specific Python version, writing it to a `.python-version` file.
* **Example:**
  ```bash
  uv python pin 3.12
  ```

---

## 5. Global Command-Line Tools (`uv tool`)
Replaces `pipx` for installing command-line utilities (like Ruff, Black, AWS CLI) globally in isolated environments.

### `uv tool install`
Installs a command-line tool globally so you can call it anywhere on your terminal.
* **Example:**
  ```bash
  uv tool install ruff
  ```
  *Now you can run the `ruff` command directly from your terminal.*

### `uv tool run`
Runs a command-line tool on the fly in an ephemeral environment without installing it permanently.
* **Example:**
  ```bash
  uv tool run black --check .
  ```
  *Temporarily downloads `black`, runs the format check on your current directory, and discards black afterwards.*

---