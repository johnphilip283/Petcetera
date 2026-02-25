# API Reference

## API Reference (Express)

**Base URL (local):** `http://localhost:5000`

All endpoints currently use **GET** and return JSON. Most responses are shaped like:

```json
{ "data": [ ... ] }
```

Errors are often returned as raw error objects via `res.send(err)`.

### Users

#### GET `/users`
Returns all users.

**SQL**: `SELECT * FROM USER;`

**Example**:

```bash
curl http://localhost:5000/users
```

#### GET `/users/:id`
Returns user by `user_id`.

**Example**:

```bash
curl http://localhost:5000/users/2
```

#### GET `/users/:id/pets`
Returns pets owned by the user.

**Response fields**: `pet_id`, `name`, `age`, `description`, `species_name`

**Example**:

```bash
curl http://localhost:5000/users/2/pets
```

#### GET `/users/:id/listings`
Returns all listings created by the given user.

**Example**:

```bash
curl http://localhost:5000/users/2/listings
```

#### GET `/users/:id/preferences`
Returns species preferences for a sitter.

**Example**:

```bash
curl http://localhost:5000/users/1/preferences
```

---

### Pets

#### GET `/pets`
Returns all pets.

```bash
curl http://localhost:5000/pets
```

#### GET `/pets/:id`
Returns details for a pet by `pet_id`.

```bash
curl http://localhost:5000/pets/1
```

#### GET `/pets/:id/photos`
Returns photo blobs for a pet.

```bash
curl http://localhost:5000/pets/1/photos
```

#### GET `/pets/add?name=...&age=...&description=...&owner_id=...&species_id=...`
Adds a pet.

**Query parameters**:
- `name` (string)
- `age` (number)
- `description` (string)
- `owner_id` (number)
- `species_id` (number)

**Example**:

```bash
curl "http://localhost:5000/pets/add?name=Buddy&age=2&description=Friendly&owner_id=2&species_id=1"
```

**Response**:

```json
{
  "message": "Successfully added pet.",
  "data": { "affectedRows": 1, "insertId": 7, ... }
}
```

> Note: This endpoint is a mutation over GET and interpolates user input directly into SQL.

---

### Species

#### GET `/species`
Returns all species.

```bash
curl http://localhost:5000/species
```

---

### Listings

#### GET `/listings`
Returns all job listings (requests) with joined owner/pet/species info.

```bash
curl http://localhost:5000/listings
```

#### GET `/listings/create?owner_id=...&title=...&start=...&end=...&pet_id=...&wage=...&description=...`
Creates a listing.

**Query parameters**:
- `owner_id` (number)
- `title` (string)
- `start` (date string: `YYYY-MM-DD`)
- `end` (date string: `YYYY-MM-DD`)
- `pet_id` (number)
- `wage` (number)
- `description` (string)

**Example**:

```bash
curl "http://localhost:5000/listings/create?owner_id=2&title=Need%20a%20sitter&start=2019-03-01&end=2019-03-03&pet_id=1&wage=25&description=Feed%20and%20walk"
```

DB triggers will reject invalid ranges or negative wages.

---

### Sitters & Ratings

#### GET `/sitters`
Returns all users with `is_sitter = 1` and their average rating.

```bash
curl http://localhost:5000/sitters
```

Response includes: `user_id`, `name`, `city`, `email`, `phone_number`, `avg_rating`

#### GET `/ratings/:id`
Returns all ratings where `ratee_id = :id`.

```bash
curl http://localhost:5000/ratings/1
```

Response fields include: `stars`, `rating_date`, `description`, `rater_id`, `rater_name`

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

