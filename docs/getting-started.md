# Getting Started

## Getting Started

### Prerequisites

- Node.js (compatible with CRA 2.1.x; Node 10/12 era works best)
- MySQL server (local)
- npm

### 1) Install Dependencies

From repo root:

```bash
npm install
```

> Note: `package.json` contains a dependency `cords` (likely a typo for `cors`). The backend code uses `cors`. If installs fail, verify dependencies.

### 2) Create and Seed the Database

1. Ensure MySQL is running.
2. (Optional) adjust root authentication method:

```sql
-- sql_script/password.sql
ALTER USER 'root'@'localhost'
  IDENTIFIED WITH mysql_native_password BY '';
```

3. Create schema and tables:

```bash
mysql -u root -p < sql_script/petsitting_create.sql
```

4. Seed data:

```bash
mysql -u root -p < sql_script/petsitting_insert.sql
```

Database name is `petsitting`.

### 3) Start the Backend API

The server entrypoint is `index.js` at repo root.

Because `package.json` does not define a server script, start it manually:

```bash
node index.js
```

You should see:

```
potato on port 5000
```

For autoreload during development, use nodemon (install globally or as dev dependency):

```bash
npx nodemon index.js
```

### 4) Start the React Frontend

In another terminal:

```bash
npm start
```

Open:

- Frontend: `http://localhost:3000`
- Backend: `http://localhost:5000`

### 5) Verify API Connectivity

Try:

- `http://localhost:5000/listings`
- `http://localhost:5000/users`
- `http://localhost:5000/species`

You should receive JSON with a `data` array.

