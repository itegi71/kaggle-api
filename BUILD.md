Build and test instructions
==========================

This repository uses `hatch` (with `hatchling`) and requires Python 3.11+.

Quick commands (copy-paste)

1. Create and activate a venv (Linux/macOS)

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip setuptools wheel
```

2. Install `hatch` (option A: pip, option B: pipx)

```bash
pip install hatch
# OR
pip install pipx
pipx install hatch
```

3. Install development dependencies (this will call the project's helper scripts):

```bash
hatch run install-deps
```

4. Compile generated sources (builds `kaggle/` from `src/`):

```bash
hatch run compile
```

5. Install into the hatch environment for running the CLI:

```bash
hatch run install
# then run the CLI inside hatch env
hatch run kaggle -v
# or open a shell inside the hatch env
hatch shell
```

Alternative local workflow

```bash
# from repository root
pip install -e .
```

Run tests
---------

- Unit tests (no Kaggle credentials required for many tests, but some tests call authenticate during import):

```bash
# If the test imports call authenticate() on import, create a dummy credentials file
# and set KAGGLE_CONFIG_DIR to point to it so imports don't exit:
mkdir -p test_runs
printf '%s' '{"username":"dummy","key":"dummy"}' > test_runs/kaggle.json

# Run the unit tests (verbose)
. .venv/bin/activate
KAGGLE_CONFIG_DIR=$(pwd)/test_runs pytest tests/unit_tests.py -vv
```

- Full test script (wrapper around `tools/GeneratePythonLibrary.sh --test local`):

```bash
hatch run test
```

Notes and troubleshooting
-------------------------
- Python version: `pyproject.toml` requires `>=3.11`. Use `python3.11` to create the venv when possible.
- `hatch run install-deps` runs several OS-specific commands (some use `sudo apt-get`) — run in an environment where you can install packages or adjust the helper scripts.
- Integration tests require valid Kaggle credentials. See `docs/README.md` and the `docs/` folder for instructions on setting up `kaggle.json` (either in `~/.config/kaggle/kaggle.json` or via `KAGGLE_CONFIG_DIR`).
- Tests in `tests/` assume network access to Kaggle APIs and may fail if your environment doesn't have internet access or proper credentials. Use the dummy credential technique shown above only to avoid import-time exits; many tests still require live API access and will fail with HTTP 401/403 if credentials are invalid.

Logs
----
- I saved test logs while running in the dev container: `test_runs/pytest_unit_with_creds_verbose.log` and `test_runs/pytest_unit_with_creds.log` (if present).

If you'd like, I can:
- re-run the full test script and upload the full log output here,
- attempt to make the test-suite more hermetic (mock network calls), or
- create a `CONTRIBUTING.md` PR with this `BUILD.md` committed for others.
