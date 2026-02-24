# Architecture

## Architecture

### Repository layout

```text
repo/
  index.js                  # Express API server entrypoint (port 5000)
  package.json              # CRA scripts + deps (backend started manually)
  public/                   # CRA public assets (includes /assets)
  src/                      # React SPA source
  docs/                     # MkDocs content
  mkdocs.yaml               # MkDocs navigation + plugins
  sql_script/               # MySQL schema, triggers, seed data
```

### Runtime topology (development)

- **MySQL**: local server hosting schema `petsitting`
- **Express API**: `http://localhost:5000`
- **React dev server**: `http://localhost:3000`

The browser loads the SPA from `:3000`. The SPA calls the API at `:5000`. CORS is enabled globally in Express.

### Frontend (React + Material-UI)

- **Entry:** `src/index.js` renders the app and route tree.
- **Routing:** `react-router-dom` for navigation between pages (Home/Dashboard/Listings/Sitters).
- **Data fetching:** `fetch()` calls directly to `http://localhost:5000/...`.
- **State management:** local component state (class components) + `componentDidMount` for loading.
- **Styling:** SCSS files co-located with components; compiled by CRA via `node-sass`.

Key UI modules:

- `src/components/dashboard/Dashboard.js`: loads user/pets/listings.
- `src/components/dashboard/AddPet.js`: modal form calling `/pets/add`.
- `src/components/listings/ListingCard.js`: renders listing data.
- `src/components/dashboard/SitterPreferences.js`: checkbox UI only (no persistence).

Hard-coded “session”:

```js
// src/constants.js
export const user_id = 2;
```

### Backend (Express + mysql)

- **Entry:** `index.js` at repo root.
- **DB connection:** single `mysql.createConnection(...)` created at startup and used for all routes.
- **Endpoints:** REST-like but implemented exclusively with `app.get(...)`.
- **Response shape:** typically `{ data: [...] }`; for creates may include `{ message, data }`.

CORS is enabled globally:

```js
const cors = require('cors');
app.use(cors());
```

### Database (MySQL)

- `sql_script/petsitting_create.sql` creates schema, tables, triggers, and a `duration()` function.
- `sql_script/petsitting_insert.sql` seeds users/species/pets/requests/ratings/preferences.
- `sql_script/password.sql` sets root auth method to `mysql_native_password`.

Core entities:

- `user`: users, some are sitters (`is_sitter=1`)
- `pet`: pets owned by a user
- `request`: a listing/job request (owner, pet, date range, wage)
- `rating`: ratings given to sitters
- `species`, `preference`, `photo`

Constraint enforcement uses triggers such as:

- rating stars must be 1–5
- cannot rate yourself
- cannot rate a non-sitter
- request start date must be <= end date
- wage cannot be negative
- cannot create a request for a pet you don’t own
- user email must contain `@`

### Notable design decisions and implications

1. **GET endpoints for writes**
   - Easy to call from the browser without extra middleware.
   - But violates HTTP semantics; vulnerable to caching/prefetching issues and makes validation/body parsing awkward.

2. **String-interpolated SQL**
   - Simple but unsafe. Any user-supplied input can inject SQL.

3. **Single DB connection**
   - Works for local/dev. Production typically uses `mysql.createPool()` for concurrency and resiliency.

4. **Hard-coded runtime config**
   - Convenient for a demo, but blocks easy deployment.

