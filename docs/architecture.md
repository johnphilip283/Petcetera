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

- **Frontend (React)**
  - Entry point: `src/index.js` renders `<Routes />`
  - Uses React Router to navigate between pages like Dashboard, Listings, Sitters.
  - Uses Material-UI components and SCSS styles.
  - Data access via `fetch('http://localhost:5000/...')`

- **Backend (Express)**
  - Entry point: `index.js` at repo root
  - Enables CORS (`app.use(cors())`) for local cross-origin calls from CRA
  - Uses `mysql` Node package with a single `createConnection` object
  - Implements route handlers with raw SQL strings and returns JSON responses

- **Database (MySQL)**
  - Schema creation: `sql_script/petsitting_create.sql`
  - Seed data: `sql_script/petsitting_insert.sql`
  - Trigger-based validations for stars, date ranges, wages, etc.

### Repository layout (conceptual)

- `/index.js` — Express server
- `/src/*` — React application source
- `/public/*` — CRA static assets, `manifest.json`, images under `/assets/*`
- `/sql_script/*` — DB schema/seed scripts
- `/catalog-info.yaml` — Backstage catalog metadata

### Data flow example (Dashboard → User pets)

1. `Dashboard.componentDidMount()` calls `getUserPets()`.
2. Frontend fetches:
   ```js
   fetch(`http://localhost:5000/users/${user_id}/pets`)
   ```
3. Express route `/users/:id/pets` executes a join query against MySQL.
4. Response body:
   ```json
   { "data": [ { "pet_id": 1, "name": "Tofu", "species_name": "Dog", ... } ] }
   ```
5. React maps results to `<PetCard pet={pet} />`.

### Cross-cutting concerns

- **CORS**: enabled globally on Express.
- **Input validation**: primarily enforced at the DB layer via triggers; app layer validation is minimal.
- **Error handling**: routes generally `res.send(err)` on DB error; frontend logs errors to console.
- **Date formatting**: listing cards use `start.split('T')[0]` implying MySQL returns ISO datetime strings.

### Known production gaps

If you intend to harden this codebase:

- Replace GET mutations with POST/PUT/DELETE.
- Use parameterized queries (`?` placeholders) or an ORM.
- Add authentication/authorization.
- Add environment-based configuration (ports, DB creds).
- Introduce connection pooling (`mysql.createPool`).

