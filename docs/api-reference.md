# API Reference

## API Reference (Express)

**Base URL (dev):** `http://localhost:5000`

All endpoints are implemented as **GET**.

### Conventions

- Successful read response:

```json
{ "data": [ /* rows */ ] }
```

- Errors: the server frequently uses `res.send(err)`, so error shape depends on the MySQL driver.

---

## Users

### GET `/users`
Returns all users.

```bash
curl http://localhost:5000/users
```

### GET `/users/:id`
Returns a user by `user_id`.

**Path params**
- `id` (number)

```bash
curl http://localhost:5000/users/1
```

### GET `/users/:id/pets`
Returns pets owned by the user.

**Response fields**
- `pet_id`, `name`, `age`, `description`, `species_name`

```bash
curl http://localhost:5000/users/2/pets
```

### GET `/users/:id/listings`
Returns listings for the given user.

Notes:
- Implemented as a query over a derived table (joined listing view) filtered by `user_id`.

```bash
curl http://localhost:5000/users/2/listings
```

### GET `/users/:id/preferences`
Returns preferred species for a user.

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
Returns a pet profile with joins.

**Response fields**
- `pet_name`, `age`, `description`, `owner_name`, `species`

```bash
curl http://localhost:5000/pets/1
```

### GET `/pets/:id/photos`
Returns photo blobs for a pet.

```bash
curl http://localhost:5000/pets/1/photos
```

### GET `/pets/add`
Adds a pet via query string.

**Query params**
- `name` (string)
- `age` (number)
- `description` (string)
- `owner_id` (number)
- `species_id` (number)

```bash
curl "http://localhost:5000/pets/add?name=Buddy&age=3&description=Friendly&owner_id=2&species_id=1"
```

**Response**

```json
{ "message": "Successfully added pet.", "data": { "affectedRows": 1 } }
```

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
Returns all job listings with joined owner/pet/species.

```bash
curl http://localhost:5000/listings
```

### GET `/listings/create`
Creates a listing (request) via query string.

**Query params**
- `owner_id` (number)
- `title` (string)
- `start` (date string `YYYY-MM-DD`)
- `end` (date string `YYYY-MM-DD`)
- `pet_id` (number)
- `wage` (number)
- `description` (string)

```bash
curl "http://localhost:5000/listings/create?owner_id=2&title=Need%20cat%20sitter&start=2019-03-01&end=2019-03-05&pet_id=1&wage=25&description=Feed%20and%20play"
```

**DB constraints enforced via triggers**
- `start <= end`
- `wage >= 0`
- `pet_id` must belong to `owner_id`

---

## Sitters & Ratings

### GET `/sitters`
Returns sitter users with average rating.

**Response fields**
- `user_id`, `name`, `city`, `email`, `phone_number`, `avg_rating`

```bash
curl http://localhost:5000/sitters
```

### GET `/ratings/:id`
Returns ratings about sitter `:id`.

```bash
curl http://localhost:5000/ratings/1
```

---

## API design notes (important)

- Writes are performed via GET; in a production API you should migrate these to POST/PUT with JSON bodies.
- Many queries interpolate user input; convert to **parameterized queries** to avoid SQL injection.
- No pagination and limited server-side filtering; the frontend filter UI is largely client-side.

