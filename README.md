# LangChain Updated

A modern LangChain exploration and experimentation repository powered by [`uv`](https://github.com/astral-sh/uv) and Python 3.13+.

---

## Environment Setup & Requirements

This project uses `uv` for fast, reproducible dependency management and virtual environments.

* **Python Version**: `>=3.12` (Currently pinned to `3.13.2`)
* **Package Manager**: `uv`

---

## Quickstart

### 1. Initial Setup
Clone the repository, pin your Python version, and sync dependencies:

```powershell
# Set copy mode to prevent cross-drive hardlink issues (e.g., C: cache to D: project)
$env:UV_LINK_MODE = "copy"

# Pin Python version
uv python pin 3.13.2

# Create virtual environment and sync dependencies
uv venv --python 3.13.2 --link-mode copy
uv sync --link-mode copy
```

### 2. Jupyter Notebook / ipykernel Setup
To run notebooks like `updated_langcahin/langchain_intro.ipynb`:

```powershell
# Register the kernel for Jupyter / VS Code
uv run python -m ipykernel install --user --name langchain-updated --display-name "Python 3.13 (langchain-updated)"
```

In VS Code / Antigravity IDE:
1. Open any notebook (`.ipynb`).
2. Click the **Kernel Selector** in the top right corner.
3. Select **Python 3.13 (langchain-updated)** or `.venv (Python 3.13.2)`.

---

## Maintenance & Troubleshooting Guide

### 1. Cross-Drive Hardlink Warning (`Failed to hardlink files`)
* **Symptom**: 
  `warning: Failed to hardlink files; falling back to full copy.`
  May lead to corrupted or missing packages (e.g., `ModuleNotFoundError: No module named 'dateutil'` when importing `ipykernel`).
* **Why it happens**:
  Windows NTFS cannot create hardlinks across different drives (e.g., when the `uv` cache is on `C:` and the repository is on `D:`).
* **Fix**:
  Always use `--link-mode copy` or set the environment variable:
  ```powershell
  $env:UV_LINK_MODE = "copy"
  uv sync --link-mode copy
  ```

---

### 2. Windows File Lock Error (`Access is denied. (os error 5)`)
* **Symptom**:
  `error: failed to remove directory ...\.venv\Scripts: Access is denied. (os error 5)`
* **Why it happens**:
  When a notebook or Python file is open in the IDE, background processes (such as `run-jedi-language-server.py` or active Jupyter kernels) hold an open file lock on `.venv\Scripts\python.exe`.
* **Fix**:
  1. Close open `.ipynb` tabs or reload the IDE window, or terminate the locking process in PowerShell:
     ```powershell
     Get-Process | Where-Object { $_.Path -like "*Langchain_updated\.venv*" } | Stop-Process -Force
     ```
  2. If rebuilding the environment, rename the locked folder before creating a new one:
     ```powershell
     Move-Item .venv .venv_old
     uv venv --python 3.13.2 --link-mode copy
     uv sync --link-mode copy
     ```

---

### 3. Upgrading Python Version (e.g. to 3.13.x or newer)
To update the project's virtual environment to a new Python version:

```powershell
# 1. View available and installed Python versions
uv python list

# 2. Pin to desired version (updates .python-version)
uv python pin 3.13.2

# 3. Recreate virtual environment with the chosen Python interpreter
uv venv --python 3.13.2 --link-mode copy

# 4. Sync project dependencies
uv sync --link-mode copy

# 5. Re-register the Jupyter kernel
uv run python -m ipykernel install --user --name langchain-updated --display-name "Python 3.13 (langchain-updated)"
```

---

## Adding New Packages

Add new dependencies using `uv add`:

```powershell
# Add a regular dependency
uv add <package-name> --link-mode copy

# Add a development / notebook dependency
uv add --dev <package-name> --link-mode copy
```