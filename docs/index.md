# example-docs-generation-component — Technical Documentation

## Overview

`example-docs-generation-component` is a small full-stack monolith (single repository) consisting of:

- **React SPA** (Create React App) using **Material-UI** for UI components.
- **Express REST-like API** using the `mysql` driver.
- **MySQL database** named **`petsitting`** with schema, triggers, seed data.

The React app uses `fetch()` to call the backend at **`http://localhost:5000`**, while CRA runs its dev server on **`http://localhost:3000`**.

### Primary Features

- Browse all **pet-sitting job listings**.
- View a **dashboard** for a “logged-in” user (hard-coded via `user_id` constant):
  - their pets
  - their listings
  - sitter preferences UI (front-end only; no persistence in provided API)
- Browse **sitters** and their average rating.

### Non-Goals / Current Limitations

- No real authentication/authorization (user identity is implied via a constant).
- No tests.
- Several write operations are implemented as **GET** endpoints with query string parameters.
- SQL is built via string interpolation (SQL injection risk).
- No environment-based configuration; DB credentials are hard-coded.

