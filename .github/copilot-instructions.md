# Copilot instructions for openProject

Repository snapshot (discovered):
- Only file present: `README.md` (project description only).

What an AI coding agent should know immediately
- This repo currently contains no source code, no build files, and no CI workflows. All changes must start with discovery and a clarifying question to the repo owner.
- Primary discovery steps (run before making edits):
  - List files and hidden files: `ls -la`
  - Search for common build manifests: `rg -n "package.json|pyproject.toml|setup.py|requirements.txt|pom.xml|build.gradle|Makefile|Dockerfile|.github/workflows"`
  - Show git status and recent commits: `git status --porcelain` and `git log -n 5 --oneline`

If you find source code (next steps)
- Identify the language via manifests (see commands above). Focus changes on the detected language/tooling.
- Look for tests (common folders: `tests/`, `spec/`, `src/test`, `__tests__`). If none exist, prepare a minimal test harness alongside features you add.

Conventions and constraints specific to this repo
- None discovered yet. Do not assume frameworks or package managers — always confirm via manifests or ask the maintainer.

When proposing or making changes
- Start with a short PR description and the discovery evidence (which files you found and which commands you ran).
- Keep edits minimal and reversible: prefer adding new files over changing many unrelated files.
- If adding a language-specific toolchain (e.g., Node, Python, Java), include a short `README.md` section with run/test commands and a minimal `Makefile` or scripts to reproduce.

Examples (use these patterns in this repo)
- Add a new Python package: create `pyproject.toml`, `src/<pkg>/__init__.py`, `tests/test_smoke.py`, and update `README.md` with `python -m pytest` instructions.
- Add CI: create `.github/workflows/ci.yml` that runs the exact commands you documented in `README.md`.

When you are uncertain
- Ask the maintainer one concise question: e.g., "Which language/tooling should I use for this project? (options: Python/Node/Go/Other)"

Files to inspect first
- `README.md`

Feedback
- I created these initial instructions from the repository snapshot. Tell me what language/tooling this project should use or point me to additional files to re-run discovery.

Bootstrap with Docker (suggested)
- Goal: make the repo runnable and deployable locally via Docker with a minimal, opinionated starter (adjust after maintainer feedback).
- Recommended minimal stack: Python 3.11 + Flask (small, easy to scaffold) or Node 18 + Express if preferred by maintainer.

Suggested files to add when bootstrapping
- `Dockerfile` — container for the app (example below).
- `docker-compose.yml` — compose definition for app + optional db (Postgres) and volumes.
- `app/` — minimal app code (e.g., `app/app.py` for Flask) and `requirements.txt` or `package.json`.
- `Makefile` (optional) — small helper targets: `make build`, `make up`, `make down`, `make logs`.

Example minimal `Dockerfile` (Python / Flask)
```
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . /app
ENV FLASK_APP=app.app
EXPOSE 5000
CMD ["gunicorn", "-b", "0.0.0.0:5000", "app.app:app"]
```

Example minimal `docker-compose.yml`
```
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/app:cached
    environment:
      - FLASK_ENV=development
  # postgres:
  #   image: postgres:15
  #   environment:
  #     POSTGRES_PASSWORD: example
```

Quick developer commands
```
# Build image
docker compose build
# Start app (foreground)
docker compose up
# Start in background
docker compose up -d
# Tail logs
docker compose logs -f
# Stop and remove
docker compose down
```

Notes for the AI coding agent
- When scaffolding, add a short `README.md` section showing the exact `docker compose` commands to run (copy/paste friendly).
- Create a tiny smoke test (`tests/test_smoke.py`) that runs the app endpoint, and add `tox`/`pytest` commands in README if you add test tooling.
- Keep commits small: discovery evidence (what you ran) + one focused scaffold commit.
- Ask the maintainer which runtime (Python/Node/Go) they prefer before implementing a full scaffold.

If you want, I can scaffold the Python+Flask starter (Dockerfile, `docker-compose.yml`, minimal `app/`, `requirements.txt`, `Makefile`, and a smoke test). Reply with which stack you prefer and I'll scaffold it.
