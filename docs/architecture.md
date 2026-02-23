# Architecture

## Architecture

### High-Level Structure

```
repo/
  index.js                  # Express API server entrypoint
  package.json              # CRA dependencies + scripts (no server scripts)
  public/                   # CRA public assets (incl. /assets)
  src/                      # React SPA
  sql_script/               # MySQL schema, triggers, seed data
```

### Runtime Topology (Development)

- **MySQL** runs locally and hosts the `petsitting` schema.
- **Express API** runs on `localhost:5000`.
- **React dev server** runs on `localhost:3000`.

The browser loads the React SPA from `:3000` which calls the API at `:5000`. CORS is enabled globally on the API.

### Backend (Express + mysql)

- Single file server: `index.js`
- Connects once at startup via `mysql.createConnection`.
- Defines multiple **GET** routes that perform `connection.query(sql, cb)`.
- Returns JSON in the shape:

```json
{ "data": [ ... ] }
```

For create endpoints it may return:

```json
{ "message": "...", "data": { ...mysql result... } }
```

### Frontend (React + Material-UI)

- CRA entry: `src/index.js` renders `<Routes />`.
- Pages/containers include:
  - `Homepage`
  - `Dashboard`
  - `Listings`
  - `CreateListing`
- Components call API using `fetch()` with hard-coded base URL `http://localhost:5000`.
- Styling is a mix of global CSS and per-component SCSS.

### Database (MySQL)

SQL scripts:

- `sql_script/petsitting_create.sql` creates tables, triggers, functions.
- `sql_script/petsitting_insert.sql` seeds initial data.
- `sql_script/password.sql` configures `root` auth method.

Important: schema uses tables named `user`, `request`, etc. (some are reserved words in other SQL dialects; in MySQL `user` can also be special in some contexts—be consistent with casing and quoting).

### Data Model Summary

Key tables:

- `user(user_id, name, email, password, is_sitter, city, phone_number)`
- `species(species_id, species_name)`
- `pet(pet_id, name, age, description, owner_id, species_id)`
- `request(request_id, title, description, owner_id, pet_id, start, end, wage)`
- `rating(rating_id, stars, description, rater_id, ratee_id, rating_date)`
- `preference(user_id, species_id)`
- `photo(photo_id, pet_id, image)`

Triggers enforce constraints such as:

- ratings stars between 1 and 5
- request start <= end
- wage not negative
- cannot rate yourself
- cannot rate a non-sitter
- cannot create a request for a pet you don’t own
- email must contain `@`

