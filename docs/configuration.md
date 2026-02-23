# Configuration

## Configuration

### Backend Configuration (Current State)

In `index.js`, the MySQL connection is hard-coded:

```js
let connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'petsitting'
});
```

CORS is enabled globally:

```js
app.use(cors());
```

The server listens on port 5000:

```js
app.listen(5000, () => console.log("potato on port 5000"));
```

### Recommended Environment Variables (Suggested Improvement)

Although not implemented, a typical setup would be:

- `PORT=5000`
- `DB_HOST=localhost`
- `DB_USER=root`
- `DB_PASSWORD=`
- `DB_NAME=petsitting`

Example refactor:

```js
const connection = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
});
```

### Frontend Configuration

The frontend calls `http://localhost:5000/...` directly from components. A common improvement is to centralize a base URL (e.g., `REACT_APP_API_BASE_URL`).

Example:

```js
// src/api.js
export const API_BASE = process.env.REACT_APP_API_BASE_URL || 'http://localhost:5000';
```

Then:

```js
fetch(`${API_BASE}/listings`)
```

