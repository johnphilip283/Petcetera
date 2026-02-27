# Architecture

## Architecture

### High-level diagram

```
React SPA (localhost:3000)
   |
   | HTTP (fetch)
   v
Express API (localhost:5000)
   |
   | mysql driver
   v
MySQL (petsitting database)
```

### Repository layout (as implemented)

- `/index.js` — Express server entrypoint (API).
- `/src/` — React application source.
- `/public/` — static assets + PWA manifest.
- `/sql_script/` — schema and seed scripts.
- `/catalog-info.yaml` — Backstage catalog metadata.

This is a **single repo** with client and server loosely separated by folder conventions (monolith repo).

### Backend (Express) design
- Single Express app configured with `cors()` and a single MySQL connection.
- Endpoints are “REST-like” (resource-ish paths) but:
  - mutations are done with `GET` endpoints (`/pets/add`, `/listings/create`).
  - SQL statements are interpolated with user input.

**Backend entrypoint:** `index.js`

- MySQL connection:
  ```js
  let connection = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '',
    database: 'petsitting'
  });
  ```

- Server port:
  - API listens on **5000**.

### Frontend (React) design
- React Router routes users into:
  - landing page
  - dashboard
  - listings (browse + create)
  - sitters
- Components are mostly class-based.
- Uses Material-UI components for UI.
- Calls backend using `fetch('http://localhost:5000/...')`.

**Frontend entrypoint:** `src/index.js` (not shown in excerpt, but stated).

### Data flow example: Add Pet
1. Dashboard shows `AddPet` modal.
2. `AddPet` fetches species via `GET /species`.
3. Submit triggers `GET /pets/add?...` to insert into `pet` table.

### Data flow example: Create Listing
1. CreateListing loads user pets via `GET /users/:id/pets`.
2. Submit triggers `GET /listings/create?...` to insert into `request` table.

### Database layer
- Schema lives in `sql_script/petsitting_create.sql`.
- Seed data lives in `sql_script/petsitting_insert.sql`.
- Several triggers enforce business rules (stars range, dates order, negative wage, etc.).

Important: Backend itself does not validate these constraints; errors may surface from DB triggers.

