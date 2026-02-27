# example-docs-generation-component (Petcetera) — Technical Documentation

## Overview

`example-docs-generation-component` (repo name: **Petcetera**) is a two-tier web application for a pet-sitting marketplace.

- **Frontend:** React single-page app (SPA) using React Router and Material-UI.
- **Backend:** Express REST-like API server.
- **Database:** MySQL schema + seed scripts.

The frontend communicates with the backend via `fetch()` calls to `http://localhost:5000`, and the backend runs SQL queries directly against a local MySQL instance using the `mysql` driver.

### Key features implemented
- Browse **pet-sitting job listings**.
- Create listings for your pets.
- View a user **dashboard** (user info, pets, listings).
- Add a pet.
- Browse **sitters** and see their **ratings**.

### Out of scope / missing in current codebase
- No authentication/session management (frontend uses a constant `user_id`).
- No API input validation / sanitization.
- No tests.
- Write operations use `GET` with query parameters rather than `POST`.

### Key dependencies
- Frontend: `react`, `react-dom`, `react-router-dom`, `@material-ui/core`, `@material-ui/icons`, `node-sass`
- Backend: `express`, `cors`, `mysql`

> Note: `package.json` includes `cords` which appears to be a typo or unused; backend uses `cors`.

