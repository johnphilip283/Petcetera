# Configuration

## Configuration

### Backend configuration
Currently configured inline in `index.js`:
- MySQL host/user/password/database
- API port: `5000`

There is no `.env` support in the shown code. A typical improvement would be:
- `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`, `PORT`

### Frontend configuration
- API base URL is hard-coded in components: `http://localhost:5000/...`

Recommended improvement:
- define a single API base URL constant (e.g., `REACT_APP_API_BASE_URL`) and use it consistently.

### Backstage metadata
`catalog-info.yaml` registers this repo as a Backstage component:
- name: `example-docs-generation-component`
- type: `service`
- lifecycle: `production`
- owner: `guests`
- system: `examples`

### PWA / static assets
- `public/manifest.json` provides basic CRA PWA metadata.
- `public/assets/` contains images (logo, etc., implied by `Homepage.js`).

