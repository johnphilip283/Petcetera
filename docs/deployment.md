# Deployment

## Deployment

This repository is primarily set up for **local development**. There is no production deployment configuration (no Dockerfiles, no production build pipeline, no environment variable configuration for backend).

### Suggested deployment approach (if needed)

#### 1) Build frontend

```bash
npm run build
```

This produces a static bundle under `build/`.

#### 2) Serve frontend from Express (recommended consolidation)

Add in `index.js`:

```js
const path = require('path');

app.use(express.static(path.join(__dirname, 'build')));

app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});
```

Then run a single Node process that serves API + SPA.

#### 3) Production-ready backend changes

- Use `mysql.createPool` and proper error handling.
- Replace GET mutation endpoints with POST and JSON bodies:
  - `app.use(express.json())`
- Add configuration via environment variables.

#### 4) Database

- Run MySQL as a managed service or container.
- Apply schema migrations (consider a migration tool) instead of ad-hoc SQL scripts.

### Security considerations

Before any real deployment, address:

- SQL injection (parameterize queries)
- Authentication + authorization
- Input validation at API layer
- Secrets management (DB password)
- HTTPS and secure headers
