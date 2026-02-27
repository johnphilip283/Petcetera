# Configuration

## Configuration

### Node / Frontend

The project uses CRA defaults; there is no explicit `.env` configuration shown.

Important hard-coded constants:

- API base URL is embedded in components as `http://localhost:5000`.
- “Logged in user” is hard-coded:

```js
// src/constants.js
export const user_id = 2;
```

**Recommended**: introduce environment variables.

Example (`.env.development` for CRA):
```bash
REACT_APP_API_BASE_URL=http://localhost:5000
REACT_APP_USER_ID=2
```

Then in code:
```js
const API = process.env.REACT_APP_API_BASE_URL;
```

### Backend / MySQL

Backend connection is hard-coded in `index.js`:

```js
mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'petsitting'
});
```

**Recommended**: use environment variables.

Example:
```bash
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=
MYSQL_DATABASE=petsitting
PORT=5000
```

And read via `process.env`.

### CORS

The backend enables CORS globally:

```js
app.use(cors());
```

For production, restrict origins, methods, and headers.

---

## Static Assets / PWA Manifest

`public/manifest.json` is the default CRA manifest.

Static images are referenced by path from `/public/assets/...` (implied by constants and components):

```js
export const petImages = ['/assets/tofu.jpg', ...];
```

Ensure those assets exist under `public/assets/`.

