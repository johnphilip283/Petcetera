# Development

## Development Guide

### Scripts
From `package.json`:
- `npm start` — starts React dev server (CRA)
- `npm run build` — builds production frontend bundle
- `npm test` — CRA test runner (no tests currently)
- `npm run eject` — CRA eject

Backend is started separately via:
```bash
nodemon index.js
```

### Frontend modules

#### Dashboard module
- `Dashboard.js` loads:
  - current user: `GET /users/:id`
  - user pets: `GET /users/:id/pets`
  - user listings: `GET /users/:id/listings`
- Shows sitter preferences UI only if `user.is_sitter === 1`.

**Code excerpt:**
```js
fetch(`http://localhost:5000/users/${user_id}`)
  .then(r => r.json())
  .then(r => this.setState({ user: r.data[0] }));
```

#### AddPet modal
- Fetches species via `/species`.
- Inserts pet via `/pets/add?...`.

**Code excerpt:**
```js
fetch(`http://localhost:5000/pets/add?name=${name}&age=${age}&description=${desc}&owner_id=${user_id}&species_id=${species_id}`)
```

#### Listings module
- Listing browsing uses cards (`ListingCard`).
- Listing creation uses `CreateListing` and calls `/listings/create?...`.

#### Sitters module
- Renders average rating and rating breakdown; `Stars.js` converts a numeric rating into 5 icons.

### Backend endpoint implementation pattern
Each route follows:
1. Build a SQL string
2. `connection.query(sql, (err, results) => ...)`

Example:
```js
app.get('/species', (req, res) => {
  connection.query('SELECT * FROM species;', (err, results) => {
    if (err) return res.send(err);
    return res.json({ data: results });
  });
});
```

### Recommended improvements (practical)

#### 1) Use parameterized queries to prevent SQL injection
Current:
```js
const GET_USER = `SELECT * FROM user WHERE user_id=${id};`;
```
Safer:
```js
connection.query(
  'SELECT * FROM user WHERE user_id = ?;',
  [id],
  (err, results) => {
    if (err) return res.status(500).json({ error: String(err) });
    res.json({ data: results });
  }
);
```

#### 2) Use POST/PUT for mutations
Example: change `/pets/add` from GET to POST.

Backend:
```js
app.use(express.json());

app.post('/pets', (req, res) => {
  const { name, age, description, owner_id, species_id } = req.body;
  connection.query(
    'INSERT INTO pet (name, age, description, owner_id, species_id) VALUES (?, ?, ?, ?, ?)',
    [name, age, description, owner_id, species_id],
    (err, results) => {
      if (err) return res.status(400).json({ error: String(err) });
      res.status(201).json({ message: 'Successfully added pet.', data: results });
    }
  );
});
```

Frontend:
```js
await fetch(`${API_BASE}/pets`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name, age, description, owner_id: user_id, species_id })
});
```

#### 3) Centralize API base URL
Create `src/api.js`:
```js
export const API_BASE = process.env.REACT_APP_API_BASE_URL ?? 'http://localhost:5000';
```

#### 4) Add basic error handling UX
Most UI actions log to console but don’t refresh lists or show failures. For example, after adding a pet, refresh the pet list in `Dashboard`.

#### 5) Add tests
- Backend: `supertest` for route testing.
- Frontend: React Testing Library for component behavior.

### Database development notes
Triggers enforce key rules:
- Ratings: `stars` must be 1–5; cannot rate yourself; can only rate sitters.
- Requests: start/end ordering; wage non-negative; must list your own pet.

When adding new features, check whether the DB already enforces a rule before duplicating logic.

