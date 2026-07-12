# Spec: Registration

## Overview
Implement the backend for user registration, turning the existing `register.html`
form (name, email, password) into a working signup flow. This is the second step
on the Spendly roadmap and the first authentication feature — it lets new users
create an account backed by the `users` table established in the database setup
step, and is a prerequisite for login, profile, and expense tracking (all of
which require a logged-in user).

## Depends on
- Step 01 — Database setup (`users` table, `get_db()`, `init_db()`) — complete.

## Routes
- `GET /register` — render the registration form — public (already implemented)
- `POST /register` — validate input, create the user, start a session, redirect
  to a logged-in page — public

## Database changes
No database changes. The `users` table (`id`, `name`, `email`, `password_hash`,
`created_at`) already supports registration as defined in `database/db.py`.

## Templates
- **Create:** none
- **Modify:** `templates/register.html` — render `{{ error }}` messages already
  supported by the template; no structural changes required unless field-level
  errors are added

## Files to change
- `app.py` — change `/register` to accept `GET` and `POST`; on `POST`, validate
  input, check for duplicate email, insert the user, start a session
  (e.g. `session["user_id"]`), and redirect
- `database/db.py` — no changes expected; reuse `get_db()`

## Files to create
None.

## New dependencies
No new dependencies. Flask's built-in `session` (via `app.secret_key`) covers
session handling — no new pip packages required.

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug (`generate_password_hash` / `check_password_hash`)
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Validate `name`, `email`, `password` are present and non-empty server-side
  (client-side `required` is not sufficient)
- Enforce a minimum password length (8 characters, per the form's placeholder text)
- On duplicate email, re-render `register.html` with an `error` message instead
  of raising an unhandled exception
- Set `app.secret_key` if not already configured, required for `session` to work

## Definition of done
- [ ] Visiting `/register` still renders the existing form unchanged
- [ ] Submitting the form with valid, unique details creates a row in `users`
      with a hashed (not plaintext) password
- [ ] Submitting with an email that already exists re-renders the form with an
      error message and does not create a duplicate row
- [ ] Submitting with a missing field or a password under 8 characters
      re-renders the form with an error message
- [ ] A successful registration starts a session and redirects the user away
      from `/register`
- [ ] App starts and runs without errors (`python app.py`)
