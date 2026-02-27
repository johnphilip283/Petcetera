# Deployment

## Deployment

This repo is currently structured and configured for **local development** (hard-coded MySQL credentials, localhost API URL, no build pipeline). To deploy, plan to separate concerns and provide environment-based configuration.

### Frontend (React)

Build:
```bash
npm run build
```

This produces static assets under `build/`.

Deployment options:
- Serve `build/` via Nginx / Apache / CDN
- Or serve via Node/Express static hosting (recommended if keeping a single service)

### Backend (Express)

Recommended production changes:
- Add `PORT` support:
  ```js
  const port = process.env.PORT || 5000;
  app.listen(port, () => console.log(`Listening on ${port}`));
  ```
- Add `express.json()` and migrate create endpoints to `POST`.
- Restrict CORS origins.
- Use connection pooling.

### Database (MySQL)

- Provision MySQL (managed service or container) with schema applied.
- Store credentials in secrets manager or environment variables.

### Containerization (suggested)

A typical approach:
- `Dockerfile` for backend
- `Dockerfile` or static hosting for frontend
- `docker-compose.yml` including MySQL and applying seed SQL in dev

Example (conceptual) `docker-compose.yml` services:
- `mysql` (with volume + init scripts)
- `api` (Express)
- `web` (React)

---

## Versioning / Compatibility Notes

- CRA v2 + React 16.6 and old `node-sass` may require older Node versions.
- If upgrading Node, consider upgrading `react-scripts` and replacing `node-sass` with `sass`.

