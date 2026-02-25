# Getting Started

## Getting Started

### Prerequisites

- Node.js + npm (compatible with CRA v2 tooling)
- MySQL server running locally
- Ability to connect to MySQL as `root` with empty password (default in provided code/scripts)

### 1) Database setup

Create the schema and seed it:

```bash
# From a shell with mysql client available
mysql -u root < sql_script/petsitting_create.sql
mysql -u root < sql_script/petsitting_insert.sql
```

If your MySQL instance requires resetting root auth to native password with empty password:

```bash
mysql -u root < sql_script/password.sql
```

Expected database name: `petsitting`.

### 2) Install dependencies

From the repository root:

```bash
npm install
```

### 3) Start the backend (Express)

The backend server is defined in `/index.js` and listens on **port 5000**.

Because `package.json` scripts currently start CRA only, start the API server manually in a separate terminal:

```bash
node index.js
```

You should see:

```
potato on port 5000
```

### 4) Start the frontend (React)

In another terminal:

```bash
npm start
```

CRA runs on `http://localhost:3000`.

### 5) Verify end-to-end locally

Open the SPA and navigate:

- `/` homepage
- `/dashboard` dashboard (uses hardcoded `user_id`)

You can also verify the API directly:

```bash
curl http://localhost:5000/users
curl http://localhost:5000/listings
curl http://localhost:5000/species
```

### Local “logged in user”

The SPA uses a constant in `src/constants.js`:

```js
export const user_id = 2;
```

Change this value to simulate a different user.

