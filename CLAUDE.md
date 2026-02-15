# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Stack

Python 3.14 + FastAPI + PostgreSQL (Docker). Dependencies managed with `uv`.

## Commands

```bash
# Start the database (must be running before the API)
docker compose up -d

# Stop the database
docker compose down

# Run the API server with hot reload
uv run uvicorn main:app --reload

# Add a dependency
uv add <package>
```

Interactive API docs are available at `http://localhost:8000/docs` when the server is running.

## Architecture

All application code lives in `main.py`. The app uses **SQLModel** (built on SQLAlchemy + Pydantic) as the ORM. A module-level `engine` is created from `DATABASE_URL` at import time. Tables are created via `SQLModel.metadata.create_all(engine)` inside a `lifespan` context manager on startup.

Database sessions are provided per-request through a `get_session()` FastAPI dependency using `with Session(engine) as session: yield session`, which ensures the session is always closed, even on errors.

Database credentials are read from `.env` at startup via `load_dotenv()` into the module-level `DATABASE_URL` string.

## Environment

The `.env` file contains:
- `DATABASE_URL` — full connection string used by `create_engine()`
- `DB_USER` — database username (`fiverr_user`)
- `DB_PASSWORD` — database password (`fiverr_pass`)
