# Copilot Instructions: Task Manager API

## Project Overview
Simple Flask REST API for task management with in-memory storage, tested with pytest and deployed via GitHub Actions.

## Architecture & Components

### Core Application ([app.py](../../app.py))
- **Single-file Flask application** with two main resource endpoints:
  - `GET /` - Health check returning welcome message
  - `GET /tasks` - Retrieve all tasks (list of objects with id, title, done)
  - `POST /tasks` - Add new task (validates title required, returns 201 on success)
- **In-memory storage**: Global `tasks` list initialized with seed data. No persistence between runs.
- **Configuration**: `FLASK_DEBUG` environment variable controls debug mode (expects "0" or "1")
- **Error handling pattern**: Return JSON with "error" key + appropriate status code (400 for validation)

### Testing ([test_app.py](../../test_app.py))
- Uses pytest with Flask test client fixture
- **Key pattern**: `app.testing = True` in fixture to enable test mode, prevents real requests
- Each test uses the client fixture and verifies JSON responses + status codes
- Tests cover happy paths (200, 201) and error cases (400 for missing title)

### Dependencies ([requirments.txt](../../requirments.txt))
Note: File named `requirments.txt` (typo) - mind when referencing in code.
- `flask` - Core web framework
- `pytest` - Test runner
- `pytest-cov` - Code coverage reporting

## Developer Workflows

### Running Tests Locally
```bash
pytest -v                              # Run all tests with verbose output
pytest --cov=. --cov-report=term-missing  # Include coverage report
pytest test_app.py::test_add_task      # Run specific test
```

### Running the Server
```bash
FLASK_DEBUG=1 python app.py            # Run with debug mode
python app.py                          # Run in production mode
```

### CI/CD Pipeline ([.github/workflows/main.yml](../../.github/workflows/main.yml))
- **Trigger**: Push to `dev`, `feature-1`, `main` branches; PRs to `main`
- **Environment**: Ubuntu with Python 3.10
- **Process**: Checkout → Setup Python → Install deps → Run pytest with coverage XML + term reports
- **Coverage output**: Stored in `reports/unit-test-results.xml` and XML coverage report

## Project-Specific Patterns

1. **Status code convention**: 200 for GET success, 201 for POST/create, 400 for validation errors
2. **Task object schema**: `{"id": int, "title": str, "done": bool}`
3. **ID generation**: `len(tasks) + 1` simple sequential indexing
4. **Payload validation**: Check `data` exists and required fields present before processing
5. **JSON responses**: Always return JSON via `jsonify()`, never plain text

## Common Tasks for AI Agents

- **Add new endpoint**: Follow GET/POST pattern, validate input before mutation, use appropriate status codes
- **Extend task model**: Update seed data in `tasks`, test fixture data, and response structures
- **Modify tests**: Use existing `client` fixture; match assertion patterns (status + response content)
- **Update workflows**: Edit triggers/conditions in `on:` section; Python version in `actions/setup-python`

