# Development

## Development Guide

### Local dev workflow

1. Ensure MySQL is running and schema is created/seeded.
2. Start backend:
   ```bash
   node index.js
   ```
3. Start frontend:
   ```bash
   npm start
   ```

### Frontend (React) development

#### Routing and shell

- `src/index.js` renders `<Routes />`.
- `Navbar` provides top-level navigation; pages pass `activeTab` to highlight the current section.

#### Dashboard feature

- `Dashboard` loads three data sources on mount:
  - `GET /users/:id`
  - `GET /users/:id/pets`
  - `GET /users/:id/listings`

- The “Add Pet” flow is implemented as a modal (`AddPet`).
  - loads dropdown options via `GET /species`
  - calls `GET /pets/add?...` to insert a new pet

AddPet snippet (current):

```js
addPet = () => {
  fetch(
    `http://localhost:5000/pets/add` +
    `?name=${this.state.name}` +
    `&age=${this.state.age}` +
    `&description=${this.state.description}` +
    `&owner_id=${user_id}` +
    `&species_id=${this.state.species_id}`
  )
    .then(r => r.json())
    .then(r => console.log(r));
};
```

**Developer note:** after adding a pet, the Dashboard does not automatically refresh pet data unless you implement a callback to re-fetch `getUserPets()`.

#### Listings feature

- `ListingCard` renders listing details and assumes MySQL returns ISO-like datetime strings:

```js
{this.props.listing.start.split('T')[0]} to {this.props.listing.end.split('T')[0]}
```

- Listing filter appears UI-only per project notes (no backend filtering).

#### Sitters/Ratings feature

- Directory is expected to call `GET /sitters`.
- Ratings view is expected to call `GET /ratings/:id`.

#### Styling

- SCSS per component, e.g. `AddPet.scss`, `Dashboard.scss`.
- Material-UI theme defined in `src/styles/materialTheme.js`.

### Backend (Express) development

#### Adding a new endpoint

All routes are declared in `index.js`.

```js
app.get('/some/path', (req, res) => {
  connection.query('SELECT ...', (err, results) => {
    if (err) return res.send(err);
    return res.json({ data: results });
  });
});
```

#### Recommended hardening when extending

- Use `express.json()` and switch mutations to `POST`.
- Use parameterized queries (avoid string interpolation).
- Return explicit status codes (`400`, `404`, `500`).
- Move SQL into separate modules (e.g., `/db/queries.js`).
- Consider `mysql.createPool()` for concurrent requests.

### Testing (currently none)

Recommended testing additions:

- **Frontend:** Jest + React Testing Library.
- **Backend:** Supertest integration tests against Express.
- **Database:** run integration tests against a dedicated test schema/container.

### Troubleshooting

- **CORS errors:** confirm backend running and `app.use(cors())` is present.
- **DB connection errors:** ensure credentials match `index.js` and schema `petsitting` exists.
- **Empty UI data:** verify seed script was applied (`petsitting_insert.sql`).

