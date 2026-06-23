---
name: python-best-practices
description: >-
  Activate when the project is using python or a python related task is given. Hello world.
---

# Python Best Practices Skill

## Summary

This skill provides a reference for modern Python development best practices, covering code style, type safety, testing, packaging, and project structure. Follow these guidelines to write clean, maintainable, and performant Python code.

## Motivation

Consistent Python practices reduce bugs, improve readability, and make onboarding easier. These guidelines are aligned with community standards such as PEP 8, PEP 484, and the [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html).

## Core Guidelines

### 1. Python Version

- Target **Python 3.11+** as the minimum supported version unless constrained by project requirements.
- Use features from newer versions when available (e.g., `match` statements, `X | Y` union types, `type` aliases).

### 2. Code Style

- Follow **PEP 8** for formatting.
- Use **4 spaces** per indentation level (no tabs).
- Limit lines to **88–100 characters** (use [black](https://black.readthedocs.io/) with `--line-length 88` or similar).
- Use **single quotes** for strings unless the string contains single quotes.
- Place imports in three groups, sorted alphabetically, separated by blank lines:
  1. Standard library
  2. Third-party packages
  3. Local project imports

```python
# Good
import os
import sys

import requests

from myproject.models import User
```

### 3. Type Hints

- Use **type hints** for all function/method signatures, variables, and class attributes.
- Use the `typing` module or modern syntax (`list[str]` instead of `List[str]`).
- Use `Optional[T]` or `T | None` for nullable types.
- Use `Union[A, B]` or `A | B` for multiple types.
- Use `TypeAlias` (Python 3.10+) for complex type aliases.
- Return `None` explicitly in type hints (`-> None`), not omitted.

```python
# Good
def greet(name: str, times: int = 1) -> str:
    return f"Hello, {name}! " * times

def find_user(user_id: int) -> User | None:
    ...
```

### 4. Docstrings

- Follow the [Google docstring style](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings).

```python
def process_data(data: list[dict[str, str]]) -> list[str]:
    """Process raw data and return formatted strings.

    Args:
        data: A list of dictionaries containing raw data fields.

    Returns:
        A list of processed string values.

    Raises:
        ValueError: If data is empty.
    """
```

### 5. Error Handling

- Catch **specific exceptions**, never bare `except:`.
- Use `raise ... from ...` for exception chaining.
- Prefer early returns / guards over deep nesting.
- Define custom exception classes for domain-specific errors.

```python
# Good
try:
    value = int(user_input)
except ValueError as exc:
    raise InvalidInputError(f"Expected an integer, got: {user_input}") from exc
```

### 6. Project Structure

A typical well-structured Python project:

```
project_root/
├── pyproject.toml          # Build system, dependencies, tools config
├── README.md
├── CHANGELOG.md
├── LICENSE
├── src/                    # Or top-level packages (choose one convention)
│   └── mypackage/
│       ├── __init__.py
│       ├── module_a.py
│       └── module_b.py
├── tests/
│   ├── conftest.py
│   └── test_module_a.py
├── .gitignore
└── .editorconfig
```

### 7. Dependencies & Build

- Use **`pyproject.toml`** as the single source of truth for metadata and dependencies.
- Pin transitive dependencies for reproducible builds.
- Use [uv](https://docs.astral.sh/uv/) or [poetry](https://python-poetry.org/) for dependency management.
- Separate runtime, dev, and optional dependencies.

```toml
# Example pyproject.toml snippet
[project]
name = "mypackage"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = [
    "requests>=2.31,<3.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "pytest-cov",
    "ruff",
]

[tool.pytest.ini_options]
testpaths = ["tests"]

[tool.ruff]
line-length = 88
target-version = "py310"
```

### 8. Testing

- Use **pytest** as the test framework.
- Place tests in a `tests/` directory mirroring the source layout.
- Name test files `test_*.py` and test functions `test_*`.
- Use fixtures (`conftest.py`) for shared test setup.
- Aim for high coverage but prioritize meaningful tests over coverage percentage.
- Use parameterized tests for similar test cases.

```python
# Good
import pytest
from mypackage.module import process_data


@pytest.mark.parametrize("data,expected", [
    ([{"key": "a"}], ["a"]),
    ([{"key": "b"}, {"key": "c"}], ["b", "c"]),
])
def test_process_data(data: list[dict[str, str]], expected: list[str]) -> None:
    assert process_data(data) == expected
```

### 9. Logging

- Use the standard `logging` module, not `print()` for debugging in production code.
- Use appropriate log levels (`DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`).
- Configure logging at the application entry point.

```python
import logging

logger = logging.getLogger(__name__)

def fetch_data(url: str) -> bytes:
    logger.debug("Fetching data from %s", url)
    ...
```

### 10. Security

- Never hardcode secrets, API keys, or passwords. Use environment variables or a secrets manager.
- Validate and sanitize all user inputs.
- Use parameterized queries for database operations (no string formatting for SQL).
- Keep dependencies up to date and run security audits (`pip-audit`, `safety`).

### 11. Performance

- Profile before optimizing. Use `cProfile`, `py-spy`, or similar.
- Use list/dict comprehensions over `map()`/`filter()` when readability is equal.
- Use generators (`yield`) for large or infinite sequences.
- Prefer `str.join()` over string concatenation in loops.
- Use `collections.namedtuple` or `dataclasses` for structured data.

### 12. Async

- Use `asyncio` for I/O-bound concurrency; use `concurrent.futures.ThreadPoolExecutor` for CPU-bound tasks that don't release the GIL.
- Always annotate async functions with `async def` and include return types (`-> Coroutine[...]]`).
- Use `async with` for async context managers.

## Linting & Formatting Tools

| Tool    | Purpose                    | Common Config                     |
|---------|---------------------------|-----------------------------------|
| **Ruff**  | Linting + formatting     | `ruff check .`, `ruff format .`  |
| **mypy**  | Static type checking     | `mypy src/`                      |
| **pytest** | Testing                | `pytest`                         |
| **pre-commit** | Git hooks           | Run ruff, mypy, pytest before commit |

Recommended `pre-commit` config:

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.0
    hooks:
      - id: ruff
      - id: ruff-format
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
```

## Common Anti-Patterns to Avoid

- **Bare `except:`** — always catch specific exceptions.
- **Mutable default arguments** — use `None` and initialize inside the function.
  ```python
  # Bad
  def append_item(item, lst=[]):
      lst.append(item)
      return lst

  # Good
  def append_item(item, lst: list | None = None) -> list:
      if lst is None:
          lst = []
      lst.append(item)
      return lst
  ```
- **Global state** — prefer dependency injection over globals.
- **Deeply nested code** — use early returns and helper functions.
- **`__all__` omission** — define `__all__` in public modules to control what `from module import *` exports.
- **Magic numbers/strings** — extract them to named constants.

## Testing

When modifying Python code:

1. Run the existing test suite before and after changes: `pytest`.
2. Add tests for new functionality or bug fixes.
3. Run the linter and type checker: `ruff check .`, `ruff format --check .`, `mypy src/`.
4. Ensure all checks pass before submitting.
