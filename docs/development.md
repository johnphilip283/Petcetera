# Development

## Development Guide

### Running frontend and backend together

Because `npm start` runs only the React dev server, you typically run two processes:

Terminal 1:
```bash
node index.js
```

Terminal 2:
```bash
npm start
```

**Recommended improvement**: use `concurrently` to run both.

---

## Frontend Implementation Notes

### Routing

The SPA is bootstrapped in `src/index.js`:

```js
import Routes from './routes';
ReactDOM.render(<Routes />, document.getElementById('root'));
```

`Routes` is expected to define `react-router-dom` routes to the major feature pages.

### Dashboard data loading

`Dashboard.js` loads user info, pets, and listings on mount:

```js
componentDidMount() {
  this.getUser();
  this.getUserPets();
  this.getUserListings();
}
```

It conditionally shows sitter preferences if `user.is_sitter === 1`.

### AddPet modal

`AddPet.js`:
- Fetches species list from `GET /species` on mount.
- Submits via `GET /pets/add?...`.

Because parameters are interpolated into a URL, you should URL-encode values:

```js
const qs = new URLSearchParams({
  name,
  age,
  description,
  owner_id: user_id,
  species_id
});
fetch(`${API}/pets/add?${qs.toString()}`);
```

### Listing creation

`CreateListing.js`:
- Prefills `start`/`end` with today’s date.
- Fetches user pets for the dropdown.
- Calls `GET /listings/create` then redirects to `/listings`.

---

## Backend Implementation Notes

### Adding new endpoints

All endpoints are currently declared directly in `index.js`. A typical addition follows:

```js
app.get('/new-resource', (req, res) => {
  const SQL = 'SELECT ...';
  connection.query(SQL, (err, results) => {
    if (err) return res.status(500).json({ error: String(err) });
    res.json({ data: results });
  });
});
```

**Recommended structure** (for maintainability):
- `server/` folder
- `routes/` per resource
- `db/` module for connection pooling

### Use parameterized queries (security)

Current code uses string interpolation:

```js
const GET_USER = `SELECT * FROM user WHERE user_id=${id};`;
```

This is vulnerable to SQL injection. Prefer placeholders:

```js
connection.query(
  'SELECT * FROM user WHERE user_id = ?',
  [id],
  (err, results) => { ... }
);
```

### Use POST for creates

Endpoints like `/pets/add` and `/listings/create` should be `POST` with JSON bodies.

Example:

```js
app.use(express.json());

app.post('/pets', (req, res) => {
  const { name, age, description, owner_id, species_id } = req.body;
  connection.query(
    'INSERT INTO pet (name, age, description, owner_id, species_id) VALUES (?, ?, ?, ?, ?)',
    [name, age, description, owner_id, species_id],
    (err, result) => {
      if (err) return res.status(400).json({ error: String(err) });
      res.status(201).json({ message: 'Successfully added pet.', id: result.insertId });
    }
  );
});
```

---

## Database Development

### Recreating schema quickly

```bash
mysql -u root -p < sql_script/petsitting_create.sql
mysql -u root -p < sql_script/petsitting_insert.sql
```

(Password is empty in this project’s dev setup.)

### Domain rules to consider when developing

- `request` inserts may fail if you try to create a listing for a pet not owned by `owner_id`.
- `rating` inserts may fail if the ratee is not a sitter or if rater == ratee.

---

## Recommended Improvements (for production readiness)

- Add authentication (sessions/JWT) and replace `user_id` constant.
- Add environment-driven configuration for API base URL and MySQL credentials.
- Add validation (e.g., `zod`, `joi`, or `express-validator`).
- Replace `mysql` single connection with `mysql2` pooled connections.
- Add tests:
  - Backend: `jest` + `supertest`
  - Frontend: React Testing Library
- Add error handling middleware returning consistent JSON.

