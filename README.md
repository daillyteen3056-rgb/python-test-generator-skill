# python-test-generator — a free Claude Code skill

A [Claude Code](https://code.claude.com/docs/en/skills) skill that writes a **runnable pytest file** for any Python module, covering happy paths, edge cases and error cases. It then runs the file and fixes its own mistakes.

## Install

**macOS / Linux**

```bash
git clone --depth 1 https://github.com/daillyteen3056-rgb/python-test-generator-skill.git ~/.claude/skills/python-test-generator
```

**Windows (PowerShell)**

```powershell
git clone --depth 1 https://github.com/daillyteen3056-rgb/python-test-generator-skill.git "$env:USERPROFILE\.claude\skills\python-test-generator"
```

Claude Code picks it up automatically, so no restart is needed. If your `skills` folder did not exist when the session started, run `/reload-skills`.

To use it in a single project instead, clone into `.claude/skills/python-test-generator` inside that project.

## Use

Just ask:

- "write tests for utils.py"
- "add unit tests for `parse_date`"
- "generate pytest for this module"

Or call it directly with `/python-test-generator`.

## What it does

1. Reads the target file and lists every public function, class and method.
2. Follows your project's existing test layout, `conftest.py` and fixtures.
3. Plans happy-path, edge and error cases for each unit and uses `pytest.raises` for exact exception types.
4. Isolates side effects with `tmp_path`, `monkeypatch` and `unittest.mock`, so it never touches the network.
5. Uses `@pytest.mark.parametrize` when 3 or more cases share a shape.
6. Writes `tests/test_<module>.py` and runs it. A wrong test gets fixed. A real bug in your code gets an `xfail` marker, and the skill tells you about it.

It will not write placeholder tests, `assert True`, or expected values copied from the implementation.

## More skills

- **[Claude Code Skills Pack](https://daillyteen.gumroad.com/l/wapct)**: this skill plus 5 more dev skills (commit messages, PR descriptions, docstrings, `.env.example` generation, Dockerfile optimizer).
- **[SkillNest](https://skillnest.dev)**: a searchable directory of 580+ free Claude Code skills.

## License

[MIT](LICENSE). Independent project, not affiliated with or endorsed by Anthropic.
