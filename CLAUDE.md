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

All application code lives in `main.py`. The app uses a synchronous psycopg2 connection pattern: `get_db_connection()` opens a new connection per request and closes it manually. There is no connection pool or ORM.

Database credentials are read from `.env` at startup via `load_dotenv()` into the module-level `DATABASE_URL` string.

## Environment

Copy `.env.example` to `.env`. The Docker Compose service exposes PostgreSQL on `localhost:5432` with:
- DB: `fiverr_db`
- User: `fiverr_user`
- Password: `fiverr_pass`

The `.env` file contains:
- `DATABASE_URL` — full connection string used by `get_db_connection()`
- `DB_USER` — database username (`fiverr_user`)
- `DB_PASSWORD` — database password (`fiverr_pass`)
