# Spec: Login and Logout

## Overview
Implement the backend for signing in and signing out, turning the existing
`login.html` form (email, password) into a working session-based auth flow,
and turning the `/logout` placeholder into a real session-clearing route.
This is the third step on the Spendly roadmap — it builds on the `users`
table and session mechanism introduced in registration (Step 02), and is a
prerequisite for any route that should only be reachable by a signed-in user
(profile, expense tracking).

## Depends on
- Step 01 — Database setup (`users` table, `get_db()`) — complete.
- Step 02 — Registration (`session["user_id"]` convention, `app.secret_key`,
  `.auth-error` template pattern) — complete.

## Routes
- `GET /login` — render the sign-in form — public (already implemented)
- `POST /login` — validate credentials, start a session, redirect to a
  logged-in page — public
- `GET /logout` — clear the session, redirect to the landing page — logged-in
  (safe to call even if no session exists; just becomes a no-op redirect)

## Database changes
No database changes. Login reads existing rows from the `users` table
(`email`, `password_hash`) as defined in `database/db.py`.

## Templates
- **Create:** none
- **Modify:** `templates/login.html` — none required; it already renders
  `{% if error %}` at the top of `.auth-card`, matching the registration
  pattern

## Files to change
- `app.py`:
  - Change `/login` to accept `GET` and `POST`; on `POST`, validate input,
    look up the user by email, verify the password with
    `check_password_hash`, start a session, and redirect
  - Change `/logout` to clear `session["user_id"]` (via `session.clear()`)
    and redirect to the landing page
- `database/db.py` — no changes expected; reuse `get_db()`

## Files to create
None.

## New dependencies
No new dependencies. Reuses Flask's built-in `session` (already configured
via `app.secret_key` in Step 02) and `werkzeug.security.check_password_hash`
(same package as `generate_password_hash`, already a dependency).

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug — verify with `check_password_hash`, never
  compare plaintext
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Validate `email` and `password` are present server-side before querying
- Look up the user by (lowercased, stripped) email; if no user is found, or
  the password does not match, show one generic error — do not reveal
  whether the email exists (e.g. "Invalid email or password.") for both cases
- On successful login, redirect to `/profile` (same convention as the
  post-registration flow); `/profile` remains the Step 04 placeholder for now
- `/logout` must clear the full session (not just delete one key) so no stale
  state survives, then redirect to `/` (landing page)

## Definition of done
- [ ] Visiting `/login` still renders the existing form unchanged
- [ ] Submitting valid credentials for an existing user starts a session and
      redirects to `/profile`
- [ ] Submitting a correct email with the wrong password re-renders the form
      with "Invalid email or password." and does not start a session
- [ ] Submitting an email that doesn't exist re-renders the form with the
      same "Invalid email or password." message (no user enumeration)
- [ ] Submitting with a missing field re-renders the form with an error
- [ ] Visiting `/logout` after logging in clears the session and redirects to
      `/`; a subsequent visit to a session-dependent route no longer shows
      the user as logged in
- [ ] App starts and runs without errors (`python app.py`)
