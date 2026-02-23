# API Reference

## API Reference (Express)

Base URL (dev): `http://localhost:5000`

All endpoints are implemented as **GET**.

### Conventions

- Successful read response:

```json
{ "data": [ ... ] }
```

- Errors: the server often does `res.send(err)` (shape depends on MySQL driver).

---

## Users

### GET `/users`
Returns all users.

**Example**

```bash
curl http://localhost:5000/users
```

### GET `/users/:id`
Returns a user by `user_id`.

**Parameters**
- `:id` (number)

**Example**

```bash
curl http://localhost:5000/users/1
```

### GET `/users/:id/pets`
Returns pets owned by the user.

**Response fields**
- `pet_id`, `name`, `age`, `description`, `species_name`

**Example**

```bash
curl http://localhost:5000/users/2/pets
```

### GET `/users/:id/listings`
Returns listings for the given user.

**Notes**
- This endpoint queries a derived table of joined listing data and filters by `user_id`.

**Example**

```bash
curl http://localhost:5000/users/2/listings
```

### GET `/users/:id/preferences`
Returns preferred species for a user.

**Example**

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

**Query parameters**
- `name` (string)
- `age` (number)
- `description` (string)
- `owner_id` (number)
- `species_id` (number)

**Example**

```bash
curl "http://localhost:5000/pets/add?name=Buddy&age=3&description=Friendly&owner_id=2&species_id=1"
```

**Response**

```json
{ "message": "Successfully added pet.", "data": { "affectedRows": 1, ... } }
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

**Query parameters**
- `owner_id` (number)
- `title` (string)
- `start` (date string: `YYYY-MM-DD`)
- `end` (date string: `YYYY-MM-DD`)
- `pet_id` (number)
- `wage` (number)
- `description` (string)

**Example**

```bash
curl "http://localhost:5000/listings/create?owner_id=2&title=Need%20cat%20sitter&start=2019-03-01&end=2019-03-05&pet_id=1&wage=25&description=Feed%20and%20play"
```

**Database constraints enforced via triggers**
- `start` must be <= `end`
- `wage` must be >= 0
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

## Notes on API Design

- Write operations use **GET** (`/pets/add`, `/listings/create`) instead of POST.
- SQL queries interpolate user input directly; use parameterized queries to prevent SQL injection.
- There is no pagination/filtering endpoint for listings; the frontend “filters” are currently UI-only.

