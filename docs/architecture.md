# Architecture

## Architecture

### High-level design

```
┌──────────────────────┐          HTTP (fetch)           ┌───────────────────────┐
│   React SPA (CRA)    │  ───────────────────────────▶   │   Express API (Node)  │
│  localhost:3000      │                                  │    localhost:5000     │
└──────────────────────┘                                  └──────────┬────────────┘
                                                                     SQL
                                                                      │
                                                             ┌────────▼─────────┐
                                                             │   MySQL DB        │
                                                             │  petsitting       │
                                                             └───────────────────┘
```

### Repository structure (practical)

- `/index.js` — Express API server entry point.
- `/src/` — React application source.
  - `/src/index.js` — SPA entry point.
  - `/src/routes` — route switch (not shown in excerpts; referenced by `src/index.js`).
  - `/src/components/*` — page/feature components.
  - `/src/constants.js` — hardcoded `user_id` and local image paths.
  - `/src/styles/*` — global styles and Material-UI theme.
- `/public/` — CRA public assets (manifest, `assets/` images).
- `/sql_script/` — schema creation, seed data, and helper scripts.
- `/docs/` — MkDocs/TechDocs markdown sources.
- `/mkdocs.yaml` — TechDocs site navigation.

### Frontend architecture

- **Routing:** `src/index.js` renders `<Routes />`. Pages include:
  - `/` (Homepage)
  - `/dashboard`
  - `/listings` and `/listings/create`
  - `/sitters`
- **UI framework:** Material-UI components plus component-scoped SCSS (`node-sass`).
- **Data fetching:** Direct `fetch('http://localhost:5000/...')` calls inside component lifecycle methods.
- **State management:** Local component state (`this.state`) only (no Redux, no context).

Example: dashboard data load

```js
componentDidMount() {
  this.getUser();
  this.getUserPets();
  this.getUserListings();
}

getUserPets = () => {
  fetch(`http://localhost:5000/users/${user_id}/pets`)
    .then(r => r.json())
    .then(r => this.setState({ pets: r.data }));
};
```

### Backend architecture

- **Express app** in `/index.js`.
- **CORS** enabled globally to allow `localhost:3000` → `localhost:5000` calls.
- A single `mysql.createConnection(...)` is created and used for all queries.
- Routes are registered directly on `app`.

Current backend patterns:

- **Read endpoints** are mostly reasonable (still unparameterized).
- **Write endpoints** are GET routes with query params.
- Errors are returned as raw objects via `res.send(err)`.

### Database architecture

- `sql_script/petsitting_create.sql` creates tables:
  - `user`, `species`, `pet`, `photo`, `request` (listings), `rating`, `preference`
- Includes triggers to enforce:
  - ratings stars range (1–5)
  - `request` start date <= end date
  - wage non-negative
  - preventing self-ratings
  - valid email format containing `@`
  - ratings can only be created for sitters
  - request pet must belong to owner

These triggers provide a baseline of validation, but the app layer does not surface errors in a user-friendly way.

### Cross-cutting concerns and known gaps

- **Security:** SQL injection risk, no auth, plaintext passwords in seed data.
- **API semantics:** GET used for creation endpoints; no JSON request bodies.
- **Reliability:** single DB connection, no pooling, minimal status codes.
- **Maintainability:** API base URLs duplicated in many components.

