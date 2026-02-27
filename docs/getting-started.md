# Getting Started

## Getting Started (Local Development)

### Prerequisites
- Node.js (project expects older CRA-era versions; modern Node may require workarounds)
- npm
- MySQL Server + MySQL Workbench (or CLI)
- `nodemon` (per README)

### 1) Create and seed the MySQL database
From `sql_script/`, run in order:

1. `petsitting_create.sql`
2. `petsitting_insert.sql`

Optional: `password.sql` sets root auth to native password with empty password:
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY ''
```

### 2) Configure DB credentials
Edit the MySQL connection in `index.js`:
```js
let connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'petsitting'
});
```

### 3) Install dependencies
From the repo root:
```bash
npm install
```

### 4) Run the backend API
In one terminal:
```bash
nodemon index.js
```

The API should start on `http://localhost:5000`.

### 5) Run the frontend
In a second terminal:
```bash
npm start
```

The React app should start on `http://localhost:3000`.

### 6) Verify end-to-end connectivity
- Open `http://localhost:3000`
- Confirm API responds:
  - `http://localhost:5000/users`
  - `http://localhost:5000/listings`

### Common issues
- **MySQL connection fails:** confirm server is running and credentials match.
- **CORS errors:** backend uses `cors()` globally; ensure backend is running on port 5000.
- **Empty UI:** may be due to missing/incorrect `user_id` constant in `src/constants` (not included here).

