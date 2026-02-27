# Architecture

## Architecture

### High-level Architecture

```
┌──────────────┐      fetch()       ┌────────────────────┐     SQL      ┌───────────────┐
│ React SPA    │ ────────────────▶ │ Express API (5000)  │ ───────────▶ │ MySQL         │
│ (CRA /src)   │ ◀──────────────── │ JSON responses      │ ◀─────────── │ petsitting DB │
└──────────────┘       JSON         └────────────────────┘              └───────────────┘
```

### Frontend: React SPA

- Bootstrapped in `src/index.js`.
- Uses `react-router-dom` routing via a `Routes` component (not shown in the provided snippets) to navigate between:
  - `/` (Homepage)
  - `/dashboard`
  - `/listings`
  - `/listings/create`
  - `/sitters`
- Uses Material-UI components and SCSS modules for styling.

State management is component-local (`this.state`) with side effects done in `componentDidMount()`.

### Backend: Express API

- Implemented in repo root `index.js`.
- Uses a single MySQL connection created at startup:

```js
let connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'petsitting'
});

connection.connect(err => {
  if (err) {
    console.log(err);
    return err;
  }
});
```

- Exposes multiple REST-like endpoints for users, pets, listings, sitters, ratings.
- Responses are JSON objects with a `data` field for query results.

### Database Layer

- Schema defined in `sql_script/petsitting_create.sql`
- Seed data in `sql_script/petsitting_insert.sql`
- Constraints/triggers enforce domain rules:
  - Rating stars must be 1–5
  - Requests must have `start <= end`
  - Wage cannot be negative
  - Cannot rate yourself
  - Cannot rate a non-sitter
  - Cannot create a request for a pet you don’t own
  - Email must contain `@`

### Data Model (simplified)

- `user (user_id, name, email, password, is_sitter, city, phone_number)`
- `species (species_id, species_name)`
- `pet (pet_id, name, age, description, owner_id -> user, species_id -> species)`
- `request (request_id, title, description, owner_id -> user, pet_id -> pet, start, end, wage)`
- `rating (rating_id, stars, description, rater_id -> user, ratee_id -> user, rating_date)`
- `preference (user_id -> user, species_id -> species)`
- `photo (photo_id, pet_id -> pet, image blob)`

---

## Request / Response Shapes

The API typically returns:

```json
{ "data": [ /* row objects */ ] }
```

Some create endpoints return:

```json
{ "message": "...", "data": { /* mysql result */ } }
```

Error responses use `res.send(err)` and may be plain objects produced by the `mysql` library.

---

## Frontend Feature Areas

### Shared UI Shell
- `src/components/navbar/Navbar.js` provides top navigation.
- `src/components/home/Homepage.js` provides a basic landing page.

### Dashboard
Key files:
- `src/components/dashboard/Dashboard.js`
- `src/components/dashboard/PetCard.js`
- `src/components/dashboard/AddPet.js`
- `src/components/dashboard/SitterPreferences.js`

Flow:
- Dashboard loads current user, user pets, and user listings via:
  - `GET /users/:id`
  - `GET /users/:id/pets`
  - `GET /users/:id/listings`

### Listings
Key files:
- `src/components/listings/CreateListing.js`
- `src/components/listings/ListingCard.js`
- `src/components/listings/ListingFilter.js`

Flow:
- Create Listing fetches pets for the current user (`GET /users/:id/pets`) to populate dropdown.
- Save triggers `GET /listings/create?...`.

### Sitters / Ratings
Backend provides:
- `GET /sitters`
- `GET /ratings/:id`

Frontend components for these screens exist in the repo (not shown here), typically rendering sitter cards and star ratings.

