# API Reference

## API Reference (Express)

**Base URL (local):** `http://localhost:5000`

### Response shape

Most endpoints return:

```json
{ "data": [ ... ] }
```

Some mutation endpoints return:

```json
{ "message": "...", "data": { ...mysql result... } }
```

### Error behavior

On SQL/connection errors, many handlers do:

```js
return res.send(err)
```

So the response may be a non-JSON object or driver-specific error payload with HTTP 200. For production, return appropriate status codes (e.g., `500`) and normalized JSON.

---

## Users

### GET `/users`

Returns all users.

**SQL:** `SELECT * FROM USER;`

```bash
curl http://localhost:5000/users
```

### GET `/users/:id`

Returns user by `user_id`.

```bash
curl http://localhost:5000/users/2
```

### GET `/users/:id/pets`

Returns pets owned by the user.

**Fields:** `pet_id`, `name`, `age`, `description`, `species_name`

```bash
curl http://localhost:5000/users/2/pets
```

### GET `/users/:id/listings`

Returns all listings created by the given user.

```bash
curl http://localhost:5000/users/2/listings
```

### GET `/users/:id/preferences`

Returns species preferences for a sitter.

```bash
curl http://localhost:5000/users/1/preferences
```

---

## Pets

### GET `/pets`

Returns all pets.

```bash
curl http://localhost:5000/pets
```

### GET `/pets/:id`

Returns a pet’s details by `pet_id`.

```bash
curl http://localhost:5000/pets/1
```

### GET `/pets/:id/photos`

Returns photo BLOBs for a pet.

```bash
curl http://localhost:5000/pets/1/photos
```

### GET `/pets/add?name=...&age=...&description=...&owner_id=...&species_id=...`

Creates a pet.

**Query parameters**
- `name` (string)
- `age` (number)
- `description` (string)
- `owner_id` (number)
- `species_id` (number)

**Example**

```bash
curl "http://localhost:5000/pets/add?name=Buddy&age=2&description=Friendly&owner_id=2&species_id=1"
```

**Notes / risks**
- This is a mutation implemented as GET.
- Inputs are interpolated directly into SQL.

---

## Species

### GET `/species`

Returns all species.

```bash
curl http://localhost:5000/species
```

---

## Listings

### GET `/listings`

Returns all listings with joined owner/pet/species fields.

```bash
curl http://localhost:5000/listings
```

### GET `/listings/create?owner_id=...&title=...&start=...&end=...&pet_id=...&wage=...&description=...`

Creates a listing (request).

**Query parameters**
- `owner_id` (number)
- `title` (string)
- `start` (date string `YYYY-MM-DD`)
- `end` (date string `YYYY-MM-DD`)
- `pet_id` (number)
- `wage` (number)
- `description` (string)

**Example**

```bash
curl "http://localhost:5000/listings/create?owner_id=2&title=Need%20a%20sitter&start=2019-03-01&end=2019-03-03&pet_id=1&wage=25&description=Feed%20and%20walk"
```

**DB validations**
Triggers will reject invalid date ranges, negative wages, and pets not owned by the listing owner.

---

## Sitters & Ratings

### GET `/sitters`

Returns all users with `is_sitter = 1` and average rating.

```bash
curl http://localhost:5000/sitters
```

**Fields include:** `user_id`, `name`, `city`, `email`, `phone_number`, `avg_rating`

### GET `/ratings/:id`

Returns ratings where `ratee_id = :id`.

```bash
curl http://localhost:5000/ratings/1
```

**Fields include:** `stars`, `rating_date`, `description`, `rater_id`, `rater_name`

---

## Frontend → API usage examples

### Fetch species (AddPet)

```js
fetch(`http://localhost:5000/species`)
  .then(r => r.json())
  .then(r => this.setState({ species: r.data }));
```

### Create listing (CreateListing)

```js
fetch(
  `http://localhost:5000/listings/create` +
  `?owner_id=${user_id}` +
  `&title=${this.state.title}` +
  `&start=${this.state.start}` +
  `&end=${this.state.end}` +
  `&pet_id=${this.state.pet_id}` +
  `&wage=${this.state.wage}` +
  `&description=${this.state.description}`
)
  .then(r => r.json())
  .then(() => this.setState({ redirect: true }));
```

