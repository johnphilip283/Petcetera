# Getting Started

## Getting Started

### Prerequisites

- Node.js + npm (compatible with CRA v2 / `react-scripts@2.1.1`)
- Local MySQL server
- Ability to connect to MySQL as `root` (empty password expected by default code)

### 1) Database setup

Create schema and seed data:

```bash
mysql -u root < sql_script/petsitting_create.sql
mysql -u root < sql_script/petsitting_insert.sql
```

If needed, reset root authentication method to allow empty password (local-only):

```bash
mysql -u root < sql_script/password.sql
```

Expected database name: `petsitting`.

### 2) Install dependencies

From repository root:

```bash
npm install
```

### 3) Start the backend (Express)

In terminal 1:

```bash
node index.js
```

Expected log:

```
potato on port 5000
```

### 4) Start the frontend (React)

In terminal 2:

```bash
npm start
```

Frontend runs at: `http://localhost:3000`

### 5) Verify the API

```bash
curl http://localhost:5000/users
curl http://localhost:5000/listings
curl http://localhost:5000/species
```

### “Logged-in user” simulation

The SPA uses a hardcoded user id:

```js
// src/constants.js
export const user_id = 2;
```

Change `user_id` to simulate other users in seeded data.

