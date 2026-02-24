# Deployment

## Deployment

### Current state

No production deployment pipeline is defined.

- CRA supports `npm run build` for frontend.
- Backend has no `npm run server` script; it must be started manually.

### Option A: Serve React build from Express (single process)

1) Build frontend:

```bash
npm run build
```

2) Update Express to serve the build output:

```js
const path = require('path');

app.use(express.static(path.join(__dirname, 'build')));

app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});
```

3) Run Express (now serves both API + SPA):

```bash
node index.js
```

### Option B: Separate hosting (recommended for real deployments)

- Host React build on static hosting (S3/CloudFront, Netlify, Vercel, etc.)
- Host Express API separately (VM, container, PaaS)
- Configure:
  - CORS allowed origins
  - `REACT_APP_API_BASE_URL` pointing to the deployed API

### Production hardening checklist

- Replace GET writes with POST/PUT and JSON bodies (`app.use(express.json())`).
- Parameterize all SQL queries; consider a query builder or ORM.
- Add authentication (sessions/JWT) and authorization.
- Validate input (types, required fields, constraints) and normalize error responses.
- Move DB credentials and ports to environment variables.
- Add DB connection pooling (`mysql.createPool`).
- Add structured logging, health checks, and monitoring.
- Use migrations rather than “drop and recreate” scripts for schema evolution.

