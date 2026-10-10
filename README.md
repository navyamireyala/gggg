# Student Project Showcase

A responsive MERN application where students can publish and discover academic and personal projects. Project records are stored in MongoDB; registration, login, and project ownership are enforced by the Express API.

## Features

- Browse project cards and open individual project pages without signing in.
- Search project titles, descriptions, and technologies; filter by an exact technology.
- Register and log in with an email and password. Passwords are hashed with bcrypt.
- Create, edit, and delete your own projects. The API rejects changes to projects owned by another user.
- Validate project content and URLs in the browser and again on the API.
- Seed a real MongoDB database with sample projects for a local test account.

## Requirements

- Node.js 20.19+ (or 22.12+) and npm.
- A MongoDB server running locally, or a MongoDB Atlas connection string.

## Run in VS Code

1. Open this folder in VS Code.
2. Copy `server/.env.example` to `server/.env`.
3. Set `MONGODB_URI` to your local MongoDB URI or Atlas connection string. Set `JWT_SECRET` to a unique random value of at least 32 characters. For example, PowerShell can generate one with:

   ```powershell
   [Convert]::ToBase64String((1..48 | ForEach-Object { Get-Random -Maximum 256 }))
   ```

4. In the VS Code terminal, install the dependencies:

   ```powershell
   npm install
   npm install --prefix server
   npm install --prefix client
   ```

5. (Optional) To add sample projects, set `SEED_NAME`, `SEED_EMAIL`, and `SEED_PASSWORD` in `server/.env`, then run:

   ```powershell
   npm run seed
   ```

   Sign in later with the email and password you configured. The seed script creates its account only when it does not already exist; it does not reset an existing user's password.

6. Start the API and Vite development server together:

   ```powershell
   npm run dev
   ```

7. Open `http://localhost:5173`. The API listens on `http://localhost:5000`; `http://localhost:5000/api/health` returns its health status.

The server reads its configuration from `server/.env`. The client defaults to `http://localhost:5000/api`. To use a different API URL, create `client/.env` with `VITE_API_URL=https://your-api-host/api` and restart Vite. Update `CLIENT_URL` in `server/.env` to the exact frontend origin when deploying.

## Build and start

```powershell
npm run build
npm start
```

The production build is written to `client/dist`. The Express server provides the API; deploy the static client build to a static host or configure your deployment platform to serve it.

## REST API

All request and response bodies are JSON. For protected routes, pass `Authorization: Bearer <token>`.

| Method | Endpoint | Access | Purpose |
| --- | --- | --- | --- |
| `GET` | `/api/health` | Public | Check that the API is running |
| `POST` | `/api/auth/register` | Public | Create an account (`name`, `email`, `password`) |
| `POST` | `/api/auth/login` | Public | Sign in (`email`, `password`) |
| `GET` | `/api/auth/me` | Signed in | Get the current account |
| `GET` | `/api/projects` | Public | Browse projects; optional `search` and `technology` query parameters |
| `GET` | `/api/projects/:id` | Public | View project details |
| `POST` | `/api/projects` | Signed in | Create a project |
| `PUT` | `/api/projects/:id` | Project owner | Update a project |
| `DELETE` | `/api/projects/:id` | Project owner | Delete a project |

Project create/update bodies contain `title`, `description`, `technologies` (array or comma-separated string), `githubUrl`, and optional `demoUrl`. Names, descriptions, technologies, email addresses, password lengths, and HTTP(S) links are validated by the API. GitHub links must point to a repository path on `github.com`.

## Try it out

1. Start MongoDB and the development servers.
2. Register an account, or seed a sample account as described above and log in.
3. Publish a project with a title, a description of at least 20 characters, one or more technologies, and a GitHub repository URL. A live demo URL is optional.
4. Open `/dashboard`, search by a title or technology, and use the technology selector. The home page and dashboard are separate routes; older `/projects` links redirect to the dashboard.
5. Open one of your projects to edit or delete it. Verify that a different account can browse the project but cannot change it.

## Project layout

```text
client/
  src/
    components/     Shared navigation, project cards, and route guards
    context/        Authentication state
    pages/          Home, browse, details, forms, login, registration
    api.js          Fetch helper for the Express API
    styles.css      Responsive interface styles
server/
  src/
    config/         MongoDB connection
    middleware/     JWT authentication
    models/         Mongoose User and Project schemas
    routes/         Authentication and project REST endpoints
    seed.js         Sample user and project records
    server.js       Express application and startup
```
