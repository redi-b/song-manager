# Song Manager App

A full-stack music management application built with a modern tech stack, featuring song management, user authentication, and a responsive UI with light/dark mode support.

## Project Structure

```
song-manager-app/
├── backend/            # Express API with Prisma ORM (Git submodule)
├── frontend/           # React app with Webpack, Redux, and Emotion (Git submodule)
├── docker-compose.yml  # Docker Compose configuration
└── README.md          # Project documentation
```

## Features

- Add, edit, and manage songs
- User authentication (login/register) with JWT and cookies
- Type-safe API with TypeScript
- Custom theming with Emotion (light/dark modes)
- Dockerized for seamless local and production deployment

## Getting Started (Local Development)

### Prerequisites

- Node.js ≥ 18
- Docker & Docker Compose
- Git

### Setup Instructions

1. **Clone the Repository**

   Since the `frontend` and `backend` folders are Git submodules, clone the repository with submodules:

   ```bash
   git clone --recurse-submodules https://github.com/yourusername/song-manager-app.git
   cd song-manager-app
   ```

   If you already cloned the repository without submodules, initialize and update them:

   ```bash
   git submodule init
   git submodule update
   ```

2. **Configure Environment**

   Create a `.env` file in the `backend/` directory using the provided `.env.example`:

   ```bash
   # backend/.env.example
   DATABASE_URL=
   PORT=
   CLIENT_URL=
   JWT_SECRET=
   ```

   Example `.env` for backend:

   ```bash
   # backend/.env
   DATABASE_URL=file:./prisma/dev.db
   PORT=3030
   CLIENT_URL=http://localhost:3000
   JWT_SECRET=your_jwt_secret
   ```

   For the frontend, create a `.env` file in the `frontend/` directory to set `BASE_API_URL` or set the arg in the docker compose file:

   ```bash
   # frontend/.env
   BASE_API_URL=http://localhost:3030/api
   ```

3. **Run in Development Mode**

   Open two terminals:

   **Backend**:

   ```bash
   cd backend
   npm install
   npx prisma generate
   npx prisma migrate dev
   npm run dev
   ```

   **Frontend**:

   ```bash
   cd frontend
   npm install
   npm run start
   ```

4. **Run with Docker**

   Build and run the entire app with Docker Compose:

   ```bash
   docker-compose up --build
   ```

   This will:

   - Build the backend and frontend services
   - Serve the frontend via Nginx on port `3000`
   - Run the backend on port `3030`
   - Apply Prisma migrations
   - Persist the SQLite database in a Docker volume (`backend-db`)

   Access the app at: `http://localhost:3000`

   The `docker-compose.yml` configuration includes:

   - **Frontend Service**:
     - Builds from `./frontend`
     - Uses environment variable `BASE_API_URL` (e.g., `http://backend:3030/api` in Docker, or `http://localhost:3030/api` for local dev)
     - Maps port `3000` (host) to `80` (container)
     - Depends on the backend service
     - Restarts unless manually stopped
   - **Backend Service**:
     - Builds from `./backend`
     - Maps port `3030` (host) to `3030` (container)
     - Uses environment variables from `backend/.env` and sets `NODE_ENV=production`, `DATABASE_URL=file:./prisma/database.db`
     - Mounts a volume for persistent SQLite storage
     - Restarts unless manually stopped
   - **Volumes**:
     - `backend-db`: Persists the SQLite database at `/app/prisma`

## Database (Prisma + SQLite)

- The database is stored in `backend/prisma/dev.db` for local development or in the `backend-db` volume for Docker.
- Prisma migrations are applied automatically when using Docker.
- To create a new migration manually:

  ```bash
  cd backend
  npx prisma migrate dev --name add_new_table
  ```

## Tech Stack

### Frontend

- React + TypeScript
- Webpack (custom configuration)
- Redux Toolkit + Redux-Saga
- Emotion (theming, light/dark mode)
- Radix UI + shadcn/ui-inspired styling

### Backend

- Express.js
- Prisma ORM (SQLite, swappable with PostgreSQL)
- JWT Authentication
- Zod validation

### DevOps

- Docker & Docker Compose
- Nginx (serving frontend in production)
