# example-docs-generation-component — Technical Documentation

## Overview

`example-docs-generation-component` is a small full-stack, monorepo-style pet-sitting marketplace demo.

- **Frontend**: React Single Page App (Create React App) using **Material-UI** for UI components and `react-router-dom` for routing.
- **Backend**: Node.js **Express** API server (repo root `index.js`) connecting to a local **MySQL** database (`petsitting`).
- **Data**: SQL schema + seed scripts under `sql_script/`.
- **Integration**: The React app calls the Express server via `fetch()` at `http://localhost:5000`.

Primary user-facing features:
- **Dashboard**: shows the current user profile, pets, listings, and (if sitter) preferences; supports adding pets.
- **Listings**: browse listings, filter UI (client-side state only in current implementation), create a listing.
- **Sitters/Ratings**: browse sitters and view ratings.

Backstage metadata is included for catalog discovery via `catalog-info.yaml`.

---

## Repository Layout (as implemented)

This repo does not use a strict `/client` `/server` split.

- `index.js` — Express API server entry point
- `src/` — React SPA
- `public/` — CRA static assets + PWA manifest
- `sql_script/` — MySQL schema, seed data, and local password helper
- `catalog-info.yaml` — Backstage component descriptor

Key entry points:
- **Backend**: `index.js`
- **Frontend**: `src/index.js`

---

## Technology Stack

### Backend
- `express` — HTTP API
- `mysql` — MySQL client using a single `createConnection`
- `cors` — allows cross-origin requests from the React dev server

### Frontend
- `react`, `react-dom`
- `react-router-dom`
- `@material-ui/core`, `@material-ui/icons`
- `node-sass` — SCSS styles

### Database
- MySQL database `petsitting`
- Tables: `user`, `pet`, `request`, `rating`, `species`, `preference`, `photo`
- Triggers and constraints enforce domain rules

---

## Environments / URLs

Development defaults implied by the code:
- **API server**: `http://localhost:5000`
- **React dev server**: typically `http://localhost:3000` (CRA default)
- **MySQL**: `localhost`, user `root`, password `''` (empty string), database `petsitting`

> Note: The backend connection is hard-coded in `index.js`.

---

## Backstage Catalog Integration

`catalog-info.yaml` registers this codebase as a Backstage `Component`:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: example-docs-generation-component
  annotations:
    backstage.io/source-location: url:https://github.com/johnphilip283/Petcetera/tree/master
    catalog-builder/managed-by-portal: 'true'
spec:
  type: service
  lifecycle: production
  owner: guests
  system: examples
```

If your Backstage instance is configured to ingest GitHub locations, you can add this component location and it will appear in the catalog.

---

## Known Limitations (current implementation)

- No test suite.
- No authentication: the “current user” is hard-coded in `src/constants.js` as `user_id = 2`.
- Backend uses string interpolation for SQL and uses `GET` requests for “create” operations; this is not safe for production.
- No server-side validation or structured error model.

These are addressed in the **Development** section as recommended improvements.

