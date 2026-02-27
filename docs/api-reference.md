# API Reference

## API Reference (Express)

Base URL (dev): `http://localhost:5000`

> All endpoints are currently implemented as `GET`. “Create” operations pass parameters via query string.

### Users

#### `GET /users`
Returns all users.

**Response**
```json
{ "data": [ { "user_id": 1, "name": "...", "email": "...", "is_sitter": 1, ... } ] }
```

#### `GET /users/:id`
Returns a single user by `user_id`.

**Example**
```bash
curl http://localhost:5000/users/2
```

#### `GET /users/:id/pets`
Returns pets owned by the user.

**Response rows include**
- `pet_id`
- `name`
- `age`
- `description`
- `species_name`

#### `GET /users/:id/listings`
Returns listings (requests) belonging to the user.

**Response rows include**
- `request_id`, `title`, `start`, `end`, `city`
- `description`, `wage`
- `pet_id`, `pet_name`, `species`
- `owner` (user name)

#### `GET /users/:id/preferences`
Returns sitter species preferences for a user.

**Response rows include**
- `species_name`

---

### Pets

#### `GET /pets`
Returns all pets.

#### `GET /pets/:id`
Returns detailed info about a pet.

Includes owner and species.

#### `GET /pets/:id/photos`
Returns photo blobs for a pet.

**Response**
```json
{ "data": [ { "image": "<blob>" } ] }
```

#### `GET /pets/add`
Adds a pet.

**Query parameters**
- `name` (string)
- `age` (number)
- `description` (string)
- `owner_id` (number)
- `species_id` (number)

**Example**
```bash
curl "http://localhost:5000/pets/add?name=Buddy&age=4&description=Friendly&owner_id=2&species_id=1"
```

**Response**
```json
{ "message": "Successfully added pet.", "data": { "affectedRows": 1, "insertId": 7, ... } }
```

---

### Species

#### `GET /species`
Returns all species.

---

### Listings (Requests)

#### `GET /listings`
Returns all job listings.

Each row joins `request`, `user`, `pet`, `species`.

#### `GET /listings/create`
Creates a listing.

**Query parameters**
- `owner_id` (number)
- `title` (string)
- `start` (YYYY-MM-DD)
- `end` (YYYY-MM-DD)
- `pet_id` (number)
- `wage` (number)
- `description` (string)

**Example**
```bash
curl "http://localhost:5000/listings/create?owner_id=2&title=Weekend%20sitter&start=2026-03-01&end=2026-03-03&pet_id=1&wage=25&description=Feed%20and%20walk"
```

**DB enforcement**
- `start` must be before or equal to `end`
- wage cannot be negative
- `pet_id` must belong to `owner_id` (trigger)

---

### Sitters

#### `GET /sitters`
Returns all users where `is_sitter = 1` with average rating.

**Fields**
- `user_id`, `name`, `city`, `email`, `phone_number`
- `avg_rating` (rounded to 2 decimals)

---

### Ratings

#### `GET /ratings/:id`
Returns ratings about a sitter (`ratee_id = :id`).

**Fields**
- `stars`, `rating_date`, `description`
- `rater_id`, `rater_name`

---

## API Usage From Frontend (examples)

### Example: Fetch species for AddPet modal

```js
fetch('http://localhost:5000/species')
  .then(r => r.json())
  .then(({ data }) => this.setState({ species: data }));
```

### Example: Create a listing

```js
fetch(`http://localhost:5000/listings/create?owner_id=${user_id}&title=${title}&start=${start}&end=${end}&pet_id=${petId}&wage=${wage}&description=${description}`)
  .then(r => r.json())
  .then(() => this.setState({ redirect: true }));
```

