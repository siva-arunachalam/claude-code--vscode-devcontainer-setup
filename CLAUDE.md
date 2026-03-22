# CLAUDE.md

This file gives Claude context about this project. Update each section as your project evolves — Claude reads this automatically at the start of every session.

---

## Project overview

<!-- Describe what this project does and why it exists. -->
<!-- Example: "A FastAPI service that processes webhook events from Stripe and stores them in Postgres." -->

TODO: describe your project here.

## Architecture

<!-- Describe the high-level structure: services, key modules, data flow. -->
<!-- Example: -->
<!-- - `src/api/` — FastAPI routes -->
<!-- - `src/workers/` — background jobs -->
<!-- - `src/db/` — SQLAlchemy models and migrations -->

TODO: describe your architecture here.

## Tech stack

<!-- List languages, frameworks, and key libraries. -->

- Language: Python 3.13
- <!-- Framework: FastAPI / Django / Flask -->
- <!-- ORM: SQLAlchemy / Django ORM -->
- <!-- Queue: Celery / ARQ -->

## Conventions

<!-- Document patterns Claude should follow when writing code for this project. -->

- Use `black` for formatting and `ruff` for linting
- Write tests with `pytest`; place them in `tests/` mirroring `src/`
- <!-- Add your own conventions here -->

## Environment

- Dev container with Docker Compose
- Secrets in `.env` (never committed); see `.env.example` for required keys
- Optional services (Postgres, Redis) are defined in `docker-compose.yml` but commented out by default — uncomment what you need

## Common tasks

<!-- Commands Claude or developers run frequently. -->

```sh
# Install dependencies
pip install -r requirements.txt

# Run tests
pytest

# Lint and format
ruff check . && black .
```

## Out of scope

<!-- Things Claude should NOT do without being explicitly asked. -->

- Do not push to remote branches
- Do not modify `.env`
- <!-- Add your own restrictions here -->
