# Development

## Development Guide

### Local development workflow

1. Start MySQL and load schema/seed (see Getting Started)
2. Start API:

```bash
npx nodemon index.js
```

3. Start React:

```bash
npm start
```

### Frontend patterns used in this repo

#### Data fetching in containers (class components)

Example from `Dashboard`:

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

#### Write operations via query strings

Example from `AddPet`:

```js
fetch(
  `http://localhost:5000/pets/add?name=${this.state.name}` +
  `&age=${this.state.age}` +
  `&description=${this.state.description}` +
  `&owner_id=${user_id}` +
  `&species_id=${this.state.species_id}`
)
```

**Important:** query strings must be encoded for user-entered fields.

```js
const name = encodeURIComponent(this.state.name);
const description = encodeURIComponent(this.state.description);

fetch(`${API_BASE}/pets/add?name=${name}&description=${description}&age=${age}&owner_id=${user_id}&species_id=${species_id}`);
```

### Backend patterns used in this repo

#### Adding a new route

Routes are declared directly in `index.js`:

```js
app.get('/resource', (req, res) => {
  connection.query('SELECT ...', (err, results) => {
    if (err) return res.send(err);
    return res.json({ data: results });
  });
});
```

#### Parameterized queries (recommended)

Current code often does:

```js
const GET_USER = `SELECT * FROM user WHERE user_id=${id};`;
```

Recommended safer version:

```js
connection.query(
  'SELECT * FROM user WHERE user_id = ?',
  [req.params.id],
  (err, results) => {
    if (err) return res.status(500).json({ error: String(err) });
    res.json({ data: results });
  }
);
```

#### Standardize errors and status codes (recommended)

Instead of `res.send(err)` everywhere, adopt a consistent shape:

```js
return res.status(500).json({
  error: 'DB_QUERY_FAILED',
  details: err.message
});
```

### Database development notes

- Schema is recreated from scratch by `petsitting_create.sql` (`drop database if exists`).
- Triggers enforce core business rules; API callers should expect insert failures if constraints are violated.

Example: listing insert can fail if `start > end` due to `check_end_start_date` trigger.

### Testing (currently absent)

No tests are present.

Suggested additions:

- Backend: `jest` + `supertest` (API route tests)
- Frontend: React Testing Library (component + integration tests)
- Optional: add CI to run lint/tests on PRs

