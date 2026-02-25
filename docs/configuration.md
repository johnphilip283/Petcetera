# Configuration

## Configuration

### Backend configuration (current)

Backend configuration is currently **hardcoded** in `index.js`:

```js
let connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'petsitting'
});
```

The server listens on port **5000**:

```js
app.listen(5000, () => console.log("potato on port 5000"));
```

CORS is enabled globally:

```js
app.use(cors());
```

### Frontend configuration (current)

- API base URL is inlined throughout components as `http://localhost:5000/...`.
- Simulated logged-in user is hardcoded:

```js
// src/constants.js
export const user_id = 2;
```

### Recommended configuration improvements

If you plan to evolve the project:

- Add `.env` support for CRA:
  - `REACT_APP_API_BASE_URL=http://localhost:5000`
- Add `.env` for backend:
  - `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`, `PORT`
- Centralize API calls in a small client module (e.g., `src/api/client.js`) instead of duplicating strings.

Example frontend API helper:

```js
// src/api/client.js
const API_BASE = process.env.REACT_APP_API_BASE_URL || 'http://localhost:5000';

export async function getJson(path) {
  const res = await fetch(`${API_BASE}${path}`);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}
```

