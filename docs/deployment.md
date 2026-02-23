# Deployment

## Deployment

### Current State

No production deployment pipeline is defined. The repo uses Create React App scripts (`start`, `build`) but does not include an Express start script in `package.json`.

### Typical Deployment Options

#### Option A: Serve React build from Express (single process)
1. Build frontend:

```bash
npm run build
```

2. In Express, serve `build/`:

```js
const path = require('path');
app.use(express.static(path.join(__dirname, 'build')));
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});
```

3. Start Express as the only server.

#### Option B: Separate frontend hosting + API hosting
- Host React build on static hosting (S3/CloudFront, Netlify, Vercel).
- Host Express API separately.
- Configure CORS and `REACT_APP_API_BASE_URL`.

### Production Hardening Checklist (Recommended)

- Replace GET writes with POST/PUT and JSON bodies (`app.use(express.json())`).
- Parameterize SQL queries.
- Add authentication (sessions/JWT) and authorization.
- Add validation and consistent error responses.
- Move DB credentials to environment variables.
- Add logging, health checks, and connection pooling (`mysql.createPool`).
- Add migrations (e.g., knex, prisma, or Flyway) instead of raw scripts.

