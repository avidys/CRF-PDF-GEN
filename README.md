# CRF-PDF-GEN Documentation

## Overview
CRF-PDF-GEN is a SvelteKit application configured for Cloudflare deployment. It combines a PostgreSQL database (via Drizzle ORM) with a lightweight session-based authentication system and includes example login and logout flows. The project uses Vitest for testing and offers scripts for development, building, and database migrations.

---

## Requirements
- **Node.js** (v18+ recommended)
- **PostgreSQL** database
- Environment variable `DATABASE_URL` pointing to the database instance

---

## Installation
```bash
pnpm install     # or npm install / yarn install
```

Project Structure

src/
 ├─ lib/
 │   └─ server/           # Database & authentication utilities
 ├─ routes/
 │   └─ demo/             # Authentication demo (login, registration, logout)
 │       └─ lucia/
 ├─ hooks.server.ts       # Request lifecycle hooks


## Database Layer

The project uses Drizzle ORM with a PostgreSQL dialect. The database connection is configured in src/lib/server/db/index.ts and expects DATABASE_URL to be set in the environment.

Schema

user table with id, optional age, username, and passwordHash

session table with id, userId (references user), and expiresAt


## Authentication

Authentication uses custom session tokens stored in the session table and tracked via an auth-session cookie.

##  Core Helpers

generateSessionToken() – creates a random session token.

createSession(token, userId) – stores session metadata and sets expiration.

validateSessionToken(token) – looks up the session, renews or deletes as needed.

setSessionTokenCookie(...) / deleteSessionTokenCookie(...) – manage cookies.


## Request Hook

src/hooks.server.ts reads the session cookie, validates it, and populates event.locals.user and event.locals.session for downstream handlers.


## Demo Login Flow
Located under src/routes/demo/lucia/login, the demo illustrates registration and login:

## Registration

Validates username and password.

Hashes password with Argon2.

Creates a new user and session, then sets the session cookie.

## Login

Validates credentials.

Verifies password against stored hash.

Generates and persists a new session token and cookie.


The protected page (src/routes/demo/lucia/+page.server.ts) redirects unauthenticated users to the login route and allows logged-in users to sign out.


## Configuration

Drizzle

Defines the schema path, Postgres dialect, and credentials:


SvelteKit

Uses the Cloudflare adapter with Vite preprocessing:


Vite & Testing

Vitest runs in two projects: a client-side suite using jsdom and a server-side suite using a Node environment.


## Running the Application

### Set environment variables

export DATABASE_URL="postgres://user:password@host:port/db"

### Start development server

npm run dev

### Access the demo

Visit http://localhost:5173/demo/lucia

### Register or log in at /demo/lucia/login

After logging in, explore the protected page and sign out via the provided form.

## Testing

Run the full test suite:

npm run test

The project uses Testing Library with Vitest for both Svelte components and server-side TypeScript tests.

## Deployment

The Cloudflare adapter is configured out of the box, enabling deployment to Cloudflare Workers. Adjust environment variables and database connections as needed for production.

Feel free to extend this documentation with specific CRF/PDF-generation features as they are implemented.




