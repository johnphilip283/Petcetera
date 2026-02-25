# Development

## Development Guide

### Running in development

- Terminal 1 (DB): ensure MySQL is running and schema is created/seeded.
- Terminal 2 (backend):
  ```bash
  node index.js
  ```
- Terminal 3 (frontend):
  ```bash
  npm start
  ```

### Frontend structure and components

#### Routing
`src/index.js` renders `<Routes />` (not included in excerpts). Expect routes for:
- `/` homepage
- `/dashboard`
- `/listings`, `/listings/create`
- `/sitters`

#### Shared UI Shell
- `Navbar` displays navigation and uses `activeTab` prop to highlight current section.
- `Homepage` is a simple landing page pointing to `/dashboard`.

#### Dashboard area
- `Dashboard` loads:
  - user: `GET /users/:id`
  - pets: `GET /users/:id/pets`
  - listings: `GET /users/:id/listings`
- `AddPet` modal:
  - loads species: `GET /species`
  - adds pet: `GET /pets/add?...`
- `SitterPreferences` is currently UI-only state; it does not persist to backend.

#### Listings area
- `ListingCard` renders listing content and uses `start.split('T')[0]` formatting.
- `ListingFilter` is currently UI-only; it does not apply filtering to API results.
- `CreateListing`:
  - loads pets for dropdown: `GET /users/:id/pets`
  - creates listing: `GET /listings/create?...`

### Backend development notes

#### Adding a new endpoint
Routes are defined directly on `app` in `index.js`. A typical pattern:

```js
app.get('/some/path', (req, res) => {
  connection.query('SELECT ...', (err, results) => {
    if (err) return res.send(err);
    return res.json({ data: results });
  });
});
```

#### Safer SQL (recommended)
Current code uses string interpolation like:

```js
const GET_USER = `SELECT * FROM user WHERE user_id=${id};`;
```

Prefer parameterized queries to avoid SQL injection:

```js
app.get('/users/:id', (req, res) => {
  connection.query(
    'SELECT * FROM user WHERE user_id = ?',
    [req.params.id],
    (err, results) => {
      if (err) return res.status(500).json({ error: String(err) });
      res.json({ data: results });
    }
  );
});
```

### Styling

SCSS is used per-component (e.g., `AddPet.scss`, `Dashboard.scss`). `node-sass` enables SCSS builds in CRA v2.

### Testing

No tests are present. Suggested additions:

- Frontend: React Testing Library + Jest
- Backend: Supertest for API endpoints
- DB: Integration tests using a dedicated test schema

### Linting

CRA provides base ESLint config (`extends: react-app`). Backend is not explicitly linted.

### Common troubleshooting

- **CORS errors**: ensure backend is running and `app.use(cors())` is enabled (it is).
- **DB connection errors**: verify MySQL credentials match `index.js`.
- **Empty UI sections**: ensure seed data has been inserted.

