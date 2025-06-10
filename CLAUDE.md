# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This project is a proxy server that translates API requests between Anthropic clients (like Claude Code) and backend Large Language Models like OpenAI's GPT series or Google's Gemini models. It uses LiteLLM for the translation and FastAPI for the web server.

## Key Technologies

- Python (>=3.10)
- FastAPI: For building the API.
- LiteLLM: For translating requests to various LLM provider APIs.
- Uvicorn: ASGI server to run the FastAPI application.
- Pydantic: For data validation and settings management.
- python-dotenv: For managing environment variables.

## Code Architecture

- `server.py`: Contains the main FastAPI application logic, including request handling, model mapping, and proxying logic to LiteLLM.
- `.env` (or `.env.example` as a template): Manages configuration such as API keys and model preferences.

The server receives requests in Anthropic's API format, translates them to the format of the configured backend (OpenAI or Gemini) via LiteLLM, sends the request to the backend, converts the response back to Anthropic format, and returns it to the client. It supports both streaming and non-streaming responses.

## Common Development Tasks

### Running the Server

To run the development server:

```bash
uv run uvicorn server:app --host 0.0.0.0 --port 8082 --reload
```

This command uses `uv` to run the `uvicorn` ASGI server, which serves the FastAPI application defined in `server.py`. The `--reload` flag enables auto-reloading on code changes. The server will be available at `http://localhost:8082`.

### Setting up Environment Variables

1.  Copy `.env.example` to `.env`:
    ```bash
    cp .env.example .env
    ```
2.  Edit `.env` to include necessary API keys and model configurations:
    *   `OPENAI_API_KEY`: Your OpenAI API key.
    *   `GEMINI_API_KEY`: Your Google AI Studio (Gemini) API key.
    *   `ANTHROPIC_API_KEY`: (Optional) For proxying *to* Anthropic models.
    *   `PREFERRED_PROVIDER`: `openai` (default) or `google`. Determines the primary backend.
    *   `BIG_MODEL`: Model to map `sonnet` requests to (e.g., `gpt-4.1`, `gemini-2.5-pro-preview-03-25`).
    *   `SMALL_MODEL`: Model to map `haiku` requests to (e.g., `gpt-4.1-mini`, `gemini-2.0-flash`).

        Model mapping logic depends on `PREFERRED_PROVIDER` and whether specified models are known OpenAI or Gemini models.

### Installing Dependencies

Dependencies are managed by `uv` using the `pyproject.toml` file. `uv run` will typically handle this automatically. If manual installation is needed:

```bash
uv pip install -r requirements.txt # Assuming requirements.txt is generated from pyproject.toml
# or directly with uv if it supports pyproject.toml directly for install in your version
uv pip install .
```

(Note: The README mentions `uv` handles dependencies when running the server, implying direct `pyproject.toml` use.)

### Testing

Currently, no specific testing commands or framework (e.g., pytest, unittest) are mentioned in the README or `pyproject.toml`. If tests are added (e.g., in `tests.py`), commands to run them should be documented here. For example, if using pytest:

```bash
uv run pytest
```

## Using with Claude Code

To connect Claude Code to this proxy:

```bash
ANTHROPIC_BASE_URL=http://localhost:8082 claude
```

This directs Claude Code to use the local proxy server for its API requests.
