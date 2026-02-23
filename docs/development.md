# Development

## Development Guide

### Running Locally

1. Start MySQL and load schema/seed.
2. Start API:

```bash
npx nodemon index.js
```

3. Start React:

```bash
npm start
```

### Frontend Component Patterns

#### Data fetching in containers
Containers like `Dashboard` and `Listings` fetch in `componentDidMount` and store results in local state:

```js
componentDidMount() {
  this.getListings();
}

getListings = _ => {
  fetch(`http://localhost:5000/listings`)
    .then(r => r.json())
    .then(r => this.setState({ listings: r.data }))
}
```

#### Forms that write to the API
`CreateListing` and `AddPet` build query strings and call “create” endpoints:

```js
fetch(`http://localhost:5000/listings/create?owner_id=${user_id}&title=${this.state.title}...`)
```

**Recommendation:** Use `encodeURIComponent` for user-entered fields to avoid malformed URLs:

```js
const title = encodeURIComponent(this.state.title);
const description = encodeURIComponent(this.state.description);
fetch(`${API_BASE}/listings/create?owner_id=${user_id}&title=${title}&description=${description}`);
```

### Backend Development Notes

#### Adding a new route
Routes are currently defined inline in `index.js`.

Example pattern:

```js
app.get('/resource', (req, res) => {
  connection.query('SELECT ...', (err, results) => {
    if (err) return res.send(err);
    return res.json({ data: results });
  });
});
```

#### Use parameterized queries (recommended)
Current code does:

```js
const GET_USER = `SELECT * FROM user WHERE user_id=${id};`;
```

Safer version:

```js
connection.query(
  'SELECT * FROM user WHERE user_id = ?',
  [id],
  (err, results) => { ... }
);
```

### Styling

- Many components have `*.scss` files co-located.
- CRA compiles SCSS via `node-sass`.

### Testing

No test framework is implemented beyond CRA defaults; there are no tests in the repo.

Suggested additions:
- Backend: Jest + supertest
- Frontend: React Testing Library

