# Getting Started

## Getting Started

### Prerequisites

- Node.js compatible with older CRA tooling (project appears CRA 2.x era; Node 10/12 often works best)
- npm
- Local MySQL server

### Install dependencies

From the repo root:

```bash
npm install
```

If install fails, verify dependencies. The docs mention `cords` may be a typo for `cors`.

### Create and seed the database

1) Start MySQL

2) (Optional) ensure root uses `mysql_native_password`:

```sql
ALTER USER 'root'@'localhost'
  IDENTIFIED WITH mysql_native_password BY '';
```

3) Create schema/tables/triggers:

```bash
mysql -u root -p < sql_script/petsitting_create.sql
```

4) Seed data:

```bash
mysql -u root -p < sql_script/petsitting_insert.sql
```

### Start the backend API

The server entrypoint is `index.js` at repo root:

```bash
node index.js
```

Expected log:

```text
potato on port 5000
```

For auto-reload during development:

```bash
npx nodemon index.js
```

### Start the React frontend

In a separate terminal:

```bash
npm start
```

Open:

- Frontend: `http://localhost:3000`
- Backend: `http://localhost:5000`

### Verify API connectivity

```bash
curl http://localhost:5000/listings
curl http://localhost:5000/users
curl http://localhost:5000/species
```

Responses should be JSON with a `data` array.

