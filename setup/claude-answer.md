# What `make test` does

`make test` runs a single command from the `Makefile`:

```make
test:
	pytest -q
```

That runs pytest in quiet mode (`-q`) from the repo root. The repo has no `pytest.ini`, `pyproject.toml`, `setup.cfg` or `conftest.py`, so pytest uses its defaults. It finds every `test_*.py` file, which right now means only `tests/test_smoke.py`. That file has two tests:

1. **`test_openapi_document_can_be_loaded`** loads `docs/openapi.yaml` with `yaml.safe_load`. It checks that the `openapi` version starts with `3.` and that `paths` isn't empty. It only confirms the contract parses and has the basic structure. It doesn't check whether the contract itself is correct; `make lint-contract` does that by running `tools/lint_contract.py`.

2. **`test_participant_files_are_present`** checks that these files exist:
   - `.claude/settings.json`
   - `.devcontainer/devcontainer.json`
   - `CLAUDE.md`
   - `Makefile`
   - `tracker/CR-2.md`
   - `tracker/README.md`

   If any are missing, the test fails with `Trūkst faili: …` ("Missing files: …") and lists them.

Compared with the other targets:

- **`make verify-setup`** is the full environment check. It checks the Python version and that the required packages are installed (fastapi, Pydantic v2, httpx, pytest, schemathesis). It also checks that the `claude` CLI is installed and that `setup/claude-answer.md` exists and isn't empty. Then it runs only the smoke test file.
- **`make test`** runs the whole test suite. It's quick now because there's only one file, but any tests you add under `tests/` later will run here too.
