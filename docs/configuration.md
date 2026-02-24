# Configuration

## Configuration

### Backend configuration (current state)

The MySQL connection in `index.js` is hard-coded:

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

Port is hard-coded to `5000`:

```js
app.listen(5000, () => console.log("potato on port 5000"));
```

### Frontend configuration (current state)

Frontend calls the API via absolute URLs embedded in components, e.g.:

```js
fetch('http://localhost:5000/listings')
```

User identity is hard-coded:

```js
// src/constants.js
export const user_id = 2;
```

### Recommended environment variables (suggested improvement)

Backend:

- `PORT=5000`
- `DB_HOST=localhost`
- `DB_USER=root`
- `DB_PASSWORD=`
- `DB_NAME=petsitting`

Example refactor:

```js
const port = process.env.PORT || 5000;

const connection = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
});

app.listen(port, () => console.log(`API on port ${port}`));
```

Frontend:

- `REACT_APP_API_BASE_URL=http://localhost:5000`

Example centralization:

```js
// src/api.js
export const API_BASE = process.env.REACT_APP_API_BASE_URL || 'http://localhost:5000';

export function getJson(path) {
  return fetch(`${API_BASE}${path}`).then(r => r.json());
}
```

Then in components:

```js
import { getJson } from '../api';

getJson('/listings').then(r => this.setState({ listings: r.data }));
```

