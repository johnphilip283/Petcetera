# API Reference

## API Reference (Express)

Base URL (local): `http://localhost:5000`

### Response shape
Most endpoints return:
```json
{ "data": [ ... ] }
```
Mutation endpoints return `message` and `data` (insert metadata).

### USERS

#### GET `/users`
Returns all users.

**SQL:** `SELECT * FROM USER;`

**Example:**
```bash
curl http://localhost:5000/users
```

#### GET `/users/:id`
Returns a single user by `user_id`.

**Example:**
```bash
curl http://localhost:5000/users/1
```

#### GET `/users/:id/pets`
Returns the pets owned by the user.

**Fields:** `pet_id`, `name`, `age`, `description`, `species_name`

**Example:**
```bash
curl http://localhost:5000/users/2/pets
```

#### GET `/users/:id/listings`
Returns all listings created by the user.

This endpoint returns listing rows including owner, pet, and species fields (via a subquery).

**Example:**
```bash
curl http://localhost:5000/users/2/listings
```

#### GET `/users/:id/preferences`
Returns species preferences for a sitter.

**Example:**
```bash
curl http://localhost:5000/users/1/preferences
```

### PETS

#### GET `/pets`
Returns all pets.

#### GET `/pets/:id`
Returns a pet profile with owner and species.

#### GET `/pets/:id/photos`
Returns images for a pet from `photo` table.

> Note: `photo.image` is a BLOB; API returns it raw inside JSON, which may not be directly renderable without encoding.

#### GET `/pets/add`
Adds a pet.

**Query parameters**
- `name` (string)
- `age` (number)
- `description` (string)
- `owner_id` (number)
- `species_id` (number)

**Example:**
```bash
curl "http://localhost:5000/pets/add?name=Rex&age=3&description=Friendly&owner_id=2&species_id=1"
```

**Important behavior**
- Uses string interpolation directly into SQL.
- Uses `GET` for insertion.

### SPECIES

#### GET `/species`
Returns all species.

**Example:**
```bash
curl http://localhost:5000/species
```

### LISTINGS

#### GET `/listings`
Returns all job listings.

Includes joined data: request + owner user + pet + species.

#### GET `/listings/create`
Creates a listing (request).

**Query parameters**
- `owner_id` (number)
- `title` (string)
- `start` (date string `YYYY-MM-DD`)
- `end` (date string `YYYY-MM-DD`)
- `pet_id` (number)
- `wage` (number)
- `description` (string)

**Example:**
```bash
curl "http://localhost:5000/listings/create?owner_id=2&title=Need%20help&start=2026-03-01&end=2026-03-03&pet_id=1&wage=25&description=Feed%20and%20walk"
```

**DB trigger constraints**
- `start` must be <= `end`
- `wage` cannot be negative
- pet must belong to owner

### SITTERS & RATINGS

#### GET `/sitters`
Returns all users marked as sitters (`is_sitter = 1`) and their average rating.

Output includes:
- `user_id`, `name`, `city`, `email`, `phone_number`, `avg_rating`

#### GET `/ratings/:id`
Returns ratings about sitter with `ratee_id = :id`.

Output includes:
- `stars`, `rating_date`, `description`, `rater_id`, `rater_name`

### Error handling
- On SQL error, endpoints do `return res.send(err)`.
- On success, endpoints return JSON with `data` or `message`.

### Security warning (as implemented)
Endpoints interpolate parameters directly into SQL:
```js
const GET_USER = `SELECT * FROM user WHERE user_id=${id};`;
```
This pattern is vulnerable to SQL injection. Prefer parameterized queries (see Development section).

