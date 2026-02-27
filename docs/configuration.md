# Configuration

## Configuration

### Backend configuration (current)

Hardcoded in `index.js`:

```js
let connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'petsitting'
});
```

Server port is hardcoded to `5000`:

```js
app.listen(5000, () => console.log("potato on port 5000"));
```

CORS is enabled globally:

```js
app.use(cors());
```

### Frontend configuration (current)

- API base URL is inlined throughout components: `http://localhost:5000/...`
- Hardcoded current user:

```js
// src/constants.js
export const user_id = 2;
```

- Images referenced by string paths served from CRA `/public/assets/*`:

```js
export const petImages = ['/assets/tofu.jpg', ...];
```

### Recommended configuration improvements

#### Use environment variables

Frontend (`.env` at repo root for CRA):

```bash
REACT_APP_API_BASE_URL=http://localhost:5000
```

Backend (`.env` for Node):

```bash
PORT=5000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=
DB_NAME=petsitting
```

#### Centralize API calls in the SPA

```js
// src/api/client.js
const API_BASE = process.env.REACT_APP_API_BASE_URL || 'http://localhost:5000';

export async function getJson(path) {
  const res = await fetch(`${API_BASE}${path}`);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}
```

#### Parameterize SQL queries (backend)

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

