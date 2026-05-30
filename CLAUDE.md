# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Run the development server (port 5001)
python app.py

# Install dependencies
pip install -r requirements.txt

# Run tests
pytest

# Run a single test file
pytest tests/test_file.py
```

## Architecture

**Spendly** is a Python Flask expense-tracking web app using server-side rendering with Jinja2 templates. No frontend build pipeline — plain HTML, CSS, and JavaScript.

- `app.py` — Flask app entry point; registers all routes; runs on port 5001
- `database/db.py` — SQLite database helpers (`get_db`, `init_db`, `seed_db`); currently a stub to be implemented; uses `row_factory` and foreign key enforcement
- `templates/` — Jinja2 templates; all extend `base.html` via `{% extends %}` / `{% block content %}`
- `static/css/style.css` — global design system (CSS custom properties for colors, spacing, typography)
- `static/css/landing.css` — landing page-specific styles
- `static/js/main.js` — placeholder for future JS

## Route Map

| URL | Status |
|---|---|
| `/` | Landing page (implemented) |
| `/register` | Form rendered (backend stub) |
| `/login` | Form rendered (backend stub) |
| `/terms`, `/privacy` | Implemented |
| `/logout`, `/profile` | Placeholder |
| `/expenses/add` | Placeholder |
| `/expenses/<id>/edit` | Placeholder |
| `/expenses/<id>/delete` | Placeholder |

## Design System

CSS variables are defined in `style.css`:
- Primary: `#1a472a` (green), Accent: `#c17f24` (orange), Danger: `#c0392b`
- Fonts: DM Serif Display (headings), DM Sans (body) — loaded from Google Fonts
- Max content width: 1200px; auth forms: 440px
- Breakpoints: 900px and 600px

## Database Layer

`database/db.py` is a stub. When implementing:
- `get_db()` should return a SQLite connection with `row_factory = sqlite3.Row` and `PRAGMA foreign_keys = ON`
- `init_db()` should use `CREATE TABLE IF NOT EXISTS`
- Database file is `expense_tracker.db` (git-ignored)
