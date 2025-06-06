## Running Automated Tests

If you make a change to the NVDA code, you should run NVDA's automated tests.
These tests help to ensure that code changes do not unintentionally break functionality that was previously working.

### Pre-commit hooks

[Pre-commit hooks](https://pre-commit.com/) can be used to automatically run linting, translatable string checks and unit tests on files staged for commit.
This will automatically apply lint fixes where possible, and will cancel the commit on lint issues and other test failures.

From a shell, [set up pre-commit scripts](https://pre-commit.com/#pre-commit-install) for your NVDA python environment:

`uv run pre-commit install`

Alternatively, set up pre-commit scripts globally:

1. `pip install pre-commit`
1. `pre-commit install --allow-missing-config`

To skip pre-commit hooks from triggering, use the `--no-verify` CLI option.
Example: `git commit -m "message" --no-verify`.

#### Manually running pre-commit hooks

You can run pre-commit hooks manually with [`pre commit run`](https://pre-commit.com/#pre-commit-run).

- You can filter files with `--files` and `--all-files`
- You can also compare two revisions:
`uv run pre-commit run --from-ref origin/master --to-ref HEAD`

### Translatable string checks

To run the translatable string checks (which check that all translatable strings have translator comments), run:

```cmd
scons checkPot
```

### Linting your changes

Our linting process involves running [Ruff](https://docs.astral.sh/ruff) to pick up Python linting issues and auto-apply fixes where possible.

[pyright](https://microsoft.github.io/pyright/) is used for static type checking.

To run the linter locally:

```cmd
runlint.bat
```

To be warned about linting errors faster, you may wish to integrate Ruff and pyright with your IDE or other development tools you are using.

### Unit Tests

Unit tests can be run with the `rununittests.bat` script.
Internally this script uses the [xmlrunner](https://github.com/pycontribs/xmlrunner) wrapper around the [unittest](https://docs.python.org/3/library/unittest.html) framework to execute the tests.
Any arguments given to `rununittests.bat` are forwarded onto xmlrunner, and then to unittest.

To run only specific unit tests, specify a pattern to match against using the `-k` option on the command line.
The `-k` option can be provided multiple times to provide multiple patterns to match against.
For example, to run only methods in the `TestMove` and `TestSelection` classes in the file `tests\unit\test_cursorManager.py` file, run this command:

```cmd
rununittests -k test_cursorManager.TestMove -k test_cursorManager.TestSelection
```

Please refer to [unittest's documentation](https://docs.python.org/3/library/unittest.html#command-line-interface) for further information on how to filter tests.

### System Tests
System tests can be run with the `runsystemtests.bat --include <TAG>` script.
To run all tests standard tests for developers use `runsystemtests.bat --include NVDA`.
Internally this script uses the Robot test framework to execute the tests.
Any arguments given to `runsystemtests.bat` are forwarded onto Robot.
For more details (including filtering and exclusion of tests) see `tests/system/readme.md`.

### License checks

NVDA uses GPLv2 which is incompatible with certain licenses like Apache.
Run `runlicensecheck.bat` to check that you don't introduce any new python dependencies with incompatible licenses.

This is configured in [pyproject.toml](../../pyproject.toml) using the [licensecheck pip package](https://github.com/FHPythonUtils/LicenseCheck).
