# example-docs-generation-component — Technical Documentation

## Overview

**example-docs-generation-component** is a local/dev-focused pet-sitting web application built as a **single repository** with a classic 3-tier setup:

- **Frontend:** React SPA (Create React App) using **React Router** and **Material-UI**.
- **Backend:** Node.js **Express** server exposing REST-like JSON endpoints.
- **Database:** Local **MySQL** schema + seed data (users, pets, species, listings/requests, ratings, preferences).

The frontend calls the backend via `fetch()` requests to `http://localhost:5000/...`. The backend executes raw SQL against MySQL using the `mysql` driver and typically returns responses of the form:

```json
{ "data": [ ... ] }
```

### Key feature areas

- **Homepage:** Landing page linking to “Login”/“Sign Up” (no real auth).
- **Dashboard:** Current user profile, their pets, and their listings; includes an **Add Pet** modal.
- **Listings:** Browse listings + create a listing.
- **Sitters/Ratings:** Sitter directory with average rating and a ratings list per sitter.

### Important constraints (current)

- **No authentication/authorization.** A hardcoded `user_id` is used in the SPA.
- **Mutations are implemented via GET** endpoints with query params (`/pets/add`, `/listings/create`).
- **SQL injection risk:** queries are built using string interpolation.
- **No tests** (frontend, backend, or DB integration).

### Backstage/TechDocs metadata

This repo includes Backstage component metadata (`catalog-info.yaml`) and MkDocs configuration (`mkdocs.yaml`) with `techdocs-core`.

