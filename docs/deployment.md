# Deployment

## Deployment

This repo is currently oriented toward local development. A production deployment would typically require separating concerns and adding configuration.

### Backend deployment considerations
- Add environment-based configuration (`PORT`, DB credentials).
- Use a connection pool (`mysql.createPool`) instead of a single connection.
- Add structured logging and proper HTTP status codes.
- Put the API behind a reverse proxy (nginx) and enable TLS.

### Frontend deployment considerations
- Build static assets:
  ```bash
  npm run build
  ```
- Serve `build/` via a static server or behind nginx.

### Example: Serve built frontend from Express (optional pattern)
If you want a single-node deployment:
1. Build the React app
2. Add in Express:
   ```js
   const path = require('path');
   app.use(express.static(path.join(__dirname, 'build')));
   app.get('*', (req, res) => {
     res.sendFile(path.join(__dirname, 'build', 'index.html'));
   });
   ```

### Database deployment considerations
- Run migrations (the SQL scripts are destructive: `drop database if exists`).
- For production, use migration tooling (e.g., Prisma Migrate, Flyway, Liquibase) and avoid dropping the DB.

### Ports
- Frontend dev: `3000`
- Backend API: `5000`
- MySQL: `3306` (default)

