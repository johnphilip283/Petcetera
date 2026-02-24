# example-docs-generation-component — Technical Documentation

## Overview

`example-docs-generation-component` is a small full-stack monorepo for a pet-sitting marketplace prototype. It contains:

- **Frontend:** React single-page application (Create React App) using **Material-UI** and `react-router-dom`.
- **Backend:** Single-file **Express** API using the `mysql` driver and enabling **CORS** for the SPA.
- **Database:** **MySQL** schema `petsitting` with tables, triggers (constraint enforcement), seed data, and helper SQL for root auth.
- **Docs site:** MkDocs + `techdocs-core` plugin (Backstage TechDocs-compatible).

### Key user flows (current behavior)

- **Dashboard:** Displays a “current user” (hard-coded) with their pets and their listings; can open a modal to add a pet.
- **Listings:** Browse listings and create a new listing.
- **Sitters:** Browse sitters and view their ratings.

### Important constraints / limitations

- No authentication/authorization: the SPA uses a hard-coded `user_id` (`src/constants.js`).
- Backend “write” operations use **GET** with query strings (`/pets/add`, `/listings/create`).
- SQL statements interpolate user input directly (SQL injection risk).
- No tests.
- Backend configuration is hard-coded (DB host/user/password, port).

