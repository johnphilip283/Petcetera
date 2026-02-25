# example-docs-generation-component — Technical Documentation

## Overview

**example-docs-generation-component** is a local/dev-focused pet-sitting web application implemented as a **single repository** with a two-tier architecture:

- **Frontend**: React single-page app (Create React App) using **React Router** and **Material-UI**.
- **Backend**: Express server exposing a set of REST-like HTTP endpoints.
- **Database**: Local **MySQL** schema + seed data providing entities such as users, pets, species, listings/requests, ratings, and preferences.

The frontend calls the backend via `fetch()` requests to `http://localhost:5000/...`. The backend runs SQL queries directly against MySQL and returns JSON `{ data: ... }` payloads.

### Key feature areas

- **Dashboard**: User profile, pets, listings; modal to add a pet.
- **Listings**: Browse listings, filter UI, create listing flow.
- **Sitters/Ratings**: Sitter directory and rating cards (via backend endpoints).
- **Shared shell**: Navbar + homepage routing.

### Important notes / constraints

- Authentication is **not implemented**. A hardcoded `user_id` constant is used on the frontend.
- Most mutations are implemented as **GET endpoints with query parameters** (e.g., `/pets/add`, `/listings/create`). This is convenient for demos but not production-safe.
- The backend queries interpolate user input directly into SQL strings; this is vulnerable to **SQL injection** and should be parameterized.
- No automated tests are present.

### Service catalog metadata

The repo includes Backstage metadata for catalog registration:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: example-docs-generation-component
  annotations:
    backstage.io/source-location: url:https://github.com/johnphilip283/Petcetera/tree/master
spec:
  type: service
  lifecycle: production
  owner: guests
  system: examples
```

