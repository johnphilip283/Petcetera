# Deployment

## Deployment

This project is primarily designed for **local development**. There is no Dockerfile, production pipeline, or environment-based configuration out of the box.

### Suggested deployment approach (minimal)

#### 1) Build the frontend

```bash
npm run build
```

#### 2) Serve the SPA from Express

Add to `index.js`:

```js
const path = require('path');

app.use(express.static(path.join(__dirname, 'build')));

app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});
```

Run a single Node process to serve both API and frontend.

#### 3) Production-grade backend upgrades

- Use `mysql.createPool()`.
- Switch create endpoints to `POST` with JSON bodies:
  ```js
  app.use(express.json());
  ```
- Add config via environment variables.
- Add request validation (e.g., `zod`, `joi`, or `express-validator`).

#### 4) Database considerations

- Run MySQL as managed DB or container.
- Replace ad-hoc SQL scripts with migrations (e.g., Knex migrations, Flyway, Liquibase).

### Security considerations before real deployment

- Fix SQL injection by parameterizing all queries.
- Implement authentication/authorization (even simple session/JWT).
- Stop storing plaintext passwords.
- Enable HTTPS, add security headers (e.g., `helmet`).
- Proper secrets management for DB credentials.

