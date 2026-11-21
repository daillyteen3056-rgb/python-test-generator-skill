---
name: python-test-generator
description: Write a runnable pytest test file for a Python module or function, covering happy paths, edge cases and error cases. Use when the user asks to write, add or generate tests / unit tests / pytest for Python code. Not for other languages or for fixing failing tests.
---

# Python test generator

Produce a single pytest file that runs green against the current code without edits.

## When to use
- "write tests for utils.py", "add unit tests for `parse_date`", "generate pytest for this module"
- The target is Python source in the repo.

## When NOT to use
- Non-Python code → say so and stop.
- The user wants an existing failing test debugged → debug instead.
- The user wants integration/e2e tests against live services → ask which services to mock first.

## Steps
1. Read the target file completely. List every public function/class/method (names not starting with `_`).
2. Check how the project already tests: look for `tests/`, `conftest.py`, `pytest.ini`/`pyproject.toml` `[tool.pytest]`, existing fixtures and naming. Follow those conventions.
3. For each public unit, plan cases:
   - **Happy path** – typical input → expected output.
   - **Edge cases** – empty, zero, None, boundaries, unicode, very large inputs, as relevant to the signature.
   - **Error cases** – inputs that should raise; assert the exact exception type with `pytest.raises`.
4. Isolate side effects: use `tmp_path` for files, `monkeypatch` for env vars/time, `unittest.mock` for network/DB/subprocess. Never hit the network.
5. Use `@pytest.mark.parametrize` when 3+ cases share a shape.
6. Write the file to `tests/test_<module>.py` (or the project's existing location/pattern).
7. Run `pytest <file> -q`. If a test fails, decide: test bug → fix the test; real code bug → keep the test, mark it `@pytest.mark.xfail(reason="...")` and tell the user.

## Output constraints
- One file, imports at top, no placeholder `pass` tests, no `assert True`.
- Test names describe behaviour: `test_parse_date_rejects_empty_string`.
- Expected values must be derived from the code's actual logic, never guessed.
- End with a 2–4 line summary: file path, number of tests, anything marked xfail.

## Example
Input: `def slugify(s: str) -> str` that lowercases, strips and replaces spaces with `-`.
```python
import pytest
from app.text import slugify

@pytest.mark.parametrize("raw,expected", [
    ("Hello World", "hello-world"),
    ("  padded  ", "padded"),
    ("", ""),
])
def test_slugify_basic(raw, expected):
    assert slugify(raw) == expected

def test_slugify_rejects_none():
    with pytest.raises(AttributeError):
        slugify(None)
```

## Anti-patterns
- Testing private helpers instead of public behaviour.
- Copying the implementation into the test to compute expected values.
- Snapshotting huge outputs instead of asserting the meaningful parts.
- Leaving tests that only pass because of mocked-away logic.
