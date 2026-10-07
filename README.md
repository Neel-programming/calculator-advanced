# Advanced Calculator

A modular command-line calculator built in Python using five design patterns, pandas-backed history, and environment-based configuration. Tested with pytest at 100% coverage, enforced by GitHub Actions.

## Features

- Six operations: add, subtract, multiply, divide, power, root
- REPL interface with continuous user interaction
- Commands: `help`, `history`, `clear`, `undo`, `redo`, `save`, `load`, `exit`
- Calculation history stored in a pandas DataFrame and persisted to CSV
- Auto-save after each calculation, configurable via environment variables
- Undo and redo across the full session
- Custom exception hierarchy with graceful error messages

## Design Patterns

| Pattern | Where | What it does |
|---|---|---|
| **Strategy** | `operations.py` | Each operation is an interchangeable class implementing `execute()` |
| **Factory** | `OperationFactory` | Creates the right operation strategy from the user's input |
| **Observer** | `history.py` | `LoggingObserver` and `AutoSaveObserver` react to each new calculation |
| **Memento** | `calculator_memento.py` | Snapshots of history power undo and redo |
| **Facade** | `Calculator` | A single class hiding config, operations, history, and undo from the REPL |

### Error handling paradigms

**LBYL (Look Before You Leap)** is used where a condition is cheap to check up front: the factory verifies an operation exists before creating it, division checks its divisor, and the history manager checks a file exists before reading it.

**EAFP (Easier to Ask Forgiveness than Permission)** is used where checking first would duplicate the work: input conversion attempts `float()` and handles the failure, and file writes attempt the operation and catch `OSError`.

## Project Structure

```
calculator-advanced/
    app/
        calculator_repl.py     Calculator facade and REPL loop
        calculation.py         Calculation records
        calculator_config.py   Environment-based configuration
        calculator_memento.py  Memento and caretaker for undo/redo
        exceptions.py          Custom exception hierarchy
        history.py             Observers and pandas history manager
        input_validators.py    Input validation helpers
        operations.py          Operation strategies and factory
    tests/                     One test module per app module
    .github/workflows/
        python-app.yml         CI: tests and 100% coverage gate
    main.py                    Entry point
    .env                       Configuration values
    requirements.txt           Dependencies
```

## Configuration

Settings are read from a `.env` file:

| Variable | Default | Purpose |
|---|---|---|
| `CALCULATOR_BASE_DIR` | `./data` | Where history files are written |
| `CALCULATOR_HISTORY_FILE` | `history.csv` | History filename |
| `CALCULATOR_MAX_HISTORY_SIZE` | `100` | Maximum calculations kept in memory |
| `CALCULATOR_PRECISION` | `10` | Decimal places in results |
| `CALCULATOR_AUTO_SAVE` | `true` | Save after every calculation |

## Setup

```bash
git clone https://github.com/Neel-programming/calculator-advanced.git
cd calculator-advanced
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Usage

```bash
python3 main.py
```

| Input | Output |
|---|---|
| `add 2 3` | `Result: 5.0` |
| `power 2 10` | `Result: 1024.0` |
| `root 27 3` | `Result: 3.0` |
| `divide 5 0` | `Division by zero is not allowed.` |
| `undo` | `Undone.` |
| `save` | `History saved.` |
| `history` | Lists every calculation this session |

## Running Tests

```bash
pytest --cov=app tests/
coverage report --fail-under=100
```

## Continuous Integration

GitHub Actions runs the test suite on every push to `main`. The build fails if any test fails or if coverage drops below 100%.