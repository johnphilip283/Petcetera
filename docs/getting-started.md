# Getting Started

## Getting Started

### Prerequisites

- Node.js + npm (compatible with CRA v2; Node 10/12 era works best with these dependency versions)
- MySQL Server (local)

### 1) Install dependencies

```bash
npm install
```

### 2) Create and seed the database

From a MySQL client (or MySQL Workbench), run:

```sql
-- (Optional) Set root password to empty string for local dev
SOURCE sql_script/password.sql;

-- Create schema
SOURCE sql_script/petsitting_create.sql;

-- Seed data
SOURCE sql_script/petsitting_insert.sql;
```

This creates database **`petsitting`** and inserts sample users, pets, listings (requests), ratings, and preferences.

### 3) Start the backend (Express)

The repo’s `package.json` `start` script starts the React dev server, not Express. To run the backend you have two common options:

#### Option A: Run Express directly

```bash
node index.js
```

You should see:

```
potato on port 5000
```

#### Option B: Add a backend dev script (recommended)

Add to `package.json`:

```json
{
  "scripts": {
    "start": "react-scripts start",
    "server": "node index.js"
  }
}
```

Then:

```bash
npm run server
```

### 4) Start the frontend (React)

In a separate terminal:

```bash
npm start
```

React will run at `http://localhost:3000` and call the API at `http://localhost:5000`.

### 5) Verify the system

Check API health by visiting:

- `http://localhost:5000/users`
- `http://localhost:5000/listings`
- `http://localhost:5000/species`

Open the app:

- `http://localhost:3000/`

---

## Local “logged in user”

The UI assumes a logged-in user via a constant:

```js
// src/constants.js
export const user_id = 2;
```

Changing this value changes which user is shown on the Dashboard and which pets/listings are used when creating resources.

