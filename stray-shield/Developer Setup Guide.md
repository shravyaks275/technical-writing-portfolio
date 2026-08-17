# Stray Shield — Developer Setup Guide

> A step-by-step guide for setting up the Stray Shield application locally, configuring the frontend and backend, connecting PostgreSQL, and verifying the development environment.

---

## 1. Overview

This guide explains how to set up and run **Stray Shield** locally for development.

The project consists of:

* **Next.js frontend** — user interface and application workflows.
* **Express.js backend** — REST API, authentication, reports, and server-side functionality.
* **PostgreSQL** — persistent database option for production-oriented deployments.
* **File-based storage** — lightweight fallback for local development.
* **TensorFlow.js ML pipeline** — image-based dog-health classification.

A typical local development environment runs the frontend and backend as two separate processes:

```text
Browser
   │
   ▼
Next.js Frontend
localhost:3000
   │
   │ REST API
   ▼
Express.js Backend
localhost:3001
   │
   ├── PostgreSQL / File Storage
   │
   └── TensorFlow.js ML Pipeline
```

---

# 2. Prerequisites

Before setting up the project, ensure the following tools are installed.

| Requirement    | Version / Recommendation         | Purpose                             |
| -------------- | -------------------------------- | ----------------------------------- |
| **Node.js**    | v16+; v18 or v20 LTS recommended | Running frontend and backend        |
| **npm**        | v7+                              | Dependency management               |
| **Git**        | Latest stable version            | Source control                      |
| **PostgreSQL** | v12+                             | Production-oriented database option |
| **VS Code**    | Recommended                      | Development environment             |

### Hardware

For comfortable local development, approximately **4–8 GB of RAM** is recommended.

### Recommended VS Code Extensions

The following extensions can improve the development experience:

* ESLint
* Prettier
* GitLens
* JavaScript / TypeScript language support

---

# 3. Clone the Repository

Clone the project from GitHub:

```bash
git clone https://github.com/shravyaks275/stray-shield-web.git
```

Navigate into the project:

```bash
cd stray-shield-web
```

The repository contains both the frontend and backend components.

---

# 4. Project Structure

The relevant high-level structure is:

```text
stray-shield-web/
│
├── app/                    # Next.js application
├── components/             # Reusable UI components
├── public/                 # Static assets
├── ml/                     # Machine-learning scripts/resources
├── prisma/                 # Prisma schema/migrations
│
├── server/                 # Express.js backend
│   ├── config/
│   ├── migration/
│   ├── seed/
│   ├── index.js
│   └── package.json
│
├── dataset/                # ML dataset
│
├── package.json            # Frontend dependencies/scripts
└── ...
```

---

# 5. Frontend Setup

The frontend is implemented using **Next.js**.

## 5.1 Install Dependencies

From the repository root:

```bash
npm install
```

---

## 5.2 Configure Environment Variables

Create a `.env.local` file in the repository root:

```text
NEXT_PUBLIC_API_URL=http://localhost:3001
```

This variable tells the frontend where the backend API is running.

### Frontend Environment Variable

| Variable              | Example                 | Description                    |
| --------------------- | ----------------------- | ------------------------------ |
| `NEXT_PUBLIC_API_URL` | `http://localhost:3001` | Base URL of the Express.js API |

> **Security note:** Only variables intentionally exposed through the `NEXT_PUBLIC_` prefix should contain values that are safe to expose to the browser. Backend secrets such as `JWT_SECRET` must not be placed in `.env.local`.

---

## 5.3 Start the Frontend

Run:

```bash
npm run dev
```

The development server should start at:

```text
http://localhost:3000
```

Open the URL in a browser to access the application.

---

## 5.4 Production Build

To create a production build:

```bash
npm run build
```

After a successful build, start the production server:

```bash
npm start
```

---

# 6. Backend Setup

The backend is implemented using **Node.js and Express.js**.

## 6.1 Navigate to the Backend

From the repository root:

```bash
cd server
```

---

## 6.2 Install Backend Dependencies

Run:

```bash
npm install
```

---

# 7. Backend Environment Configuration

The backend uses environment variables for configuration.

If the repository contains an `.env.example` file, create a local `.env` file from it:

```bash
cp .env.example .env
```

On Windows, you can create the `.env` file manually if the `cp` command is unavailable.

A development configuration can contain:

```env
PORT=3001
JWT_SECRET=your-secret-key-change-in-production
NODE_ENV=development
```

### PostgreSQL Configuration

If PostgreSQL is being used:

```env
DB_HOST=localhost
DB_PORT=5432
DB_NAME=stray_shield
DB_USER=postgres
DB_PASSWORD=yourpassword
```

Optional SSL configuration:

```env
DB_SSL=false
```

---

## 7.1 Backend Environment Variables

| Variable      | Required        | Example           | Purpose                            |
| ------------- | --------------- | ----------------- | ---------------------------------- |
| `PORT`        | Yes             | `3001`            | Port used by the API server        |
| `JWT_SECRET`  | Yes             | `your-secret-key` | Secret used for JWT authentication |
| `NODE_ENV`    | Yes             | `development`     | Application environment            |
| `DB_HOST`     | PostgreSQL only | `localhost`       | Database host                      |
| `DB_PORT`     | PostgreSQL only | `5432`            | PostgreSQL port                    |
| `DB_NAME`     | PostgreSQL only | `stray_shield`    | Database name                      |
| `DB_USER`     | PostgreSQL only | `postgres`        | Database user                      |
| `DB_PASSWORD` | PostgreSQL only | `yourpassword`    | Database password                  |
| `DB_SSL`      | Optional        | `false`           | Enables/disables PostgreSQL SSL    |

> **Important:** Never commit `.env` files containing real credentials or production secrets to GitHub.

---

# 8. Start the Backend

From the `server` directory:

```bash
npm run dev
```

The backend should start on:

```text
http://localhost:3001
```

The API is now available for requests from the Next.js frontend.

---

# 9. Database Setup

Stray Shield supports two approaches to local data storage.

## Option A — File-Based Local Storage

For lightweight local development, the server supports JSON-based storage.

This option is useful when:

* You are evaluating the application locally.
* PostgreSQL is not installed.
* You only need a quick development environment.
* You want to test frontend/backend workflows without configuring a database.

Use the repository's default configuration and follow the normal backend startup process.

> The exact behavior depends on the repository's current server configuration and storage implementation.

---

# 10. PostgreSQL Setup

PostgreSQL is the recommended option for production-oriented environments.

## 10.1 Install PostgreSQL

Install PostgreSQL for your operating system.

For macOS using Homebrew:

```bash
brew install postgresql
```

Start the PostgreSQL service:

```bash
brew services start postgresql
```

Windows and Linux users can install PostgreSQL using the official installer/package manager for their operating system.

---

## 10.2 Create the Database

Open the PostgreSQL command-line client:

```bash
psql -U postgres
```

Create the Stray Shield database:

```sql
CREATE DATABASE stray_shield;
```

Exit PostgreSQL:

```sql
\q
```

---

## 10.3 Configure Database Credentials

Update `server/.env`:

```env
DB_HOST=localhost
DB_PORT=5432
DB_NAME=stray_shield
DB_USER=postgres
DB_PASSWORD=yourpassword
DB_SSL=false
```

Ensure the credentials match the PostgreSQL installation.

---

# 11. Database Migrations and Seed Data

If the repository's `server/package.json` contains migration and seed scripts, run them from the `server` directory:

```bash
npm run migrate
```

Then:

```bash
npm run seed
```

The project also contains Prisma-related resources.

If Prisma migrations are being used for the current environment, the appropriate Prisma command can be run according to the project's Prisma configuration:

```bash
npx prisma migrate deploy
```

Before running migration commands, check the `prisma/` directory and `package.json` scripts to determine which migration workflow is currently used by the project.

---

# 12. Running the Full Application

The frontend and backend should run simultaneously.

This requires **two terminal windows**.

## Terminal 1 — Backend

From the repository root:

```bash
cd server
npm run dev
```

The backend runs at:

```text
http://localhost:3001
```

---

## Terminal 2 — Frontend

Open another terminal and return to the repository root:

```bash
cd stray-shield-web
```

Install dependencies if they have not already been installed:

```bash
npm install
```

Start the frontend:

```bash
npm run dev
```

The frontend runs at:

```text
http://localhost:3000
```

---

# 13. Access the Application

Once both servers are running, open:

**Frontend**

```text
http://localhost:3000
```

**Backend API**

```text
http://localhost:3001
```

The frontend communicates with the backend using the URL configured in:

```env
NEXT_PUBLIC_API_URL=http://localhost:3001
```

---

# 14. Verify the Installation

After starting the backend, verify that the API is responding.

## 14.1 Health Check

Run:

```bash
curl http://localhost:3001/api/health
```

A successful response should resemble:

```json
{
  "status": "OK",
  "message": "Stray Shield API is running"
}
```

---

# 15. Test Authentication

The authentication endpoints can be tested directly using `curl`.

## 15.1 Test Signup

```bash
curl -X POST http://localhost:3001/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"test123","name":"Test","phone":"1234567890","userType":"citizen"}'
```

A successful request should return the application's signup response.

---

## 15.2 Test Login

```bash
curl -X POST http://localhost:3001/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"test123","userType":"citizen"}'
```

The response should indicate whether authentication succeeded and, where applicable, provide the authentication token used for protected API requests.

> Use test credentials for local development only. Do not use real passwords or production credentials in command examples.

---

# 16. Installation Verification Checklist

Use the following checklist to confirm that the local environment is ready:

* [ ] Node.js is installed.
* [ ] Git is installed.
* [ ] Repository has been cloned.
* [ ] Frontend dependencies are installed.
* [ ] Backend dependencies are installed.
* [ ] `.env.local` has been configured.
* [ ] Backend `.env` has been configured.
* [ ] PostgreSQL is configured if database mode is being used.
* [ ] Backend starts successfully on port `3001`.
* [ ] Frontend starts successfully on port `3000`.
* [ ] `/api/health` returns a successful response.
* [ ] Signup flow works.
* [ ] Login flow works.
* [ ] Frontend can communicate with the backend.

---

# 17. Common Setup Problems

## Port Conflicts

### Symptom

The frontend or backend fails to start because the configured port is already in use.

### Expected Ports

| Service            |   Port |
| ------------------ | -----: |
| Next.js frontend   | `3000` |
| Express.js backend | `3001` |
| PostgreSQL         | `5432` |

### Solution

Identify and stop the process using the required port, or change the application's configured port.

If the backend port changes, update:

```env
NEXT_PUBLIC_API_URL=http://localhost:<new-port>
```

accordingly.

---

## Database Connection Errors

### Symptoms

* API fails to start.
* Database queries fail.
* Connection timeout errors appear.

### Checks

Verify that:

* PostgreSQL is installed.
* PostgreSQL is running.
* The database exists.
* `DB_HOST` is correct.
* `DB_PORT` is correct.
* `DB_NAME` is correct.
* `DB_USER` is correct.
* `DB_PASSWORD` is correct.

For local PostgreSQL, the typical configuration is:

```env
DB_HOST=localhost
DB_PORT=5432
```

---

## JWT Authentication Problems

### Symptoms

* Login succeeds but protected requests fail.
* Requests return authentication errors.
* Tokens cannot be validated.

### Checks

Verify that the backend has a valid:

```env
JWT_SECRET=your-secret-key
```

The same backend secret must be used consistently by the components that generate and validate JWTs.

> The JWT secret is a backend credential and should **not** be exposed through a `NEXT_PUBLIC_` environment variable.

---

## Frontend Cannot Reach Backend

### Symptoms

* API requests fail from the browser.
* Reports cannot be submitted.
* Login/signup requests fail.
* Network requests target the wrong port.

### Check

Verify `.env.local` contains:

```env
NEXT_PUBLIC_API_URL=http://localhost:3001
```

Then restart the Next.js development server so the environment variable is loaded.

---

## Large Image Upload Errors

Stray Shield supports image uploads, and the backend request body limits may affect large uploads.

The current server configuration uses a body limit of approximately **50 MB**.

If larger requests are required, review the relevant configuration in:

```text
server/index.js
```

and adjust the request limits appropriately.

Large upload limits should be increased carefully because they can increase memory usage and affect server performance.

---

# 18. Development Workflow

A typical development workflow is:

```text
Clone Repository
       ↓
Install Dependencies
       ↓
Configure Environment
       ↓
Start Database (if required)
       ↓
Start Backend
       ↓
Start Frontend
       ↓
Verify API
       ↓
Test Application
       ↓
Develop / Debug
       ↓
Build for Production
```

---

# 19. Quick Start

For an already-configured development environment, the essential commands are:

### Backend

```bash
cd server
npm install
npm run dev
```

### Frontend

In a second terminal:

```bash
npm install
npm run dev
```

Then open:

```text
http://localhost:3000
```

The backend should be available at:

```text
http://localhost:3001
```

---

# 20. Production Considerations

Before deploying Stray Shield to a production environment:

* Use strong, unique JWT secrets.
* Store secrets in the deployment platform's environment configuration.
* Do not commit `.env` files.
* Use PostgreSQL instead of file-based storage.
* Configure database SSL where required.
* Configure appropriate image-upload limits.
* Use dedicated cloud storage for uploaded images where appropriate.
* Review authentication and authorization behavior.
* Configure production CORS settings.
* Review logging and error handling.
* Build the frontend using the production configuration.
* Verify all frontend and backend environment variables.

---

## 21. Setup Complete

If the following are all working:

```text
Frontend  →  http://localhost:3000
Backend   →  http://localhost:3001
Health    →  /api/health
Database  →  Connected (if enabled)
Auth      →  Signup / Login working
```

then the local Stray Shield development environment is ready.

You can now begin developing, testing, and modifying the application.

---

> **Next:** See the [System Overview](system-overview.md) to understand Stray Shield's architecture, components, data flow, and user workflows.
