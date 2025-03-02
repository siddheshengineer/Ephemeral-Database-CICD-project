# Automated Ephemeral Database Branching using Node.js PostgreSQL Todo Application

This repository demonstrates a simple Node.js Todo application that uses PostgreSQL along with automated database migrations via `node-pg-migrate`. It also features **automated ephemeral database branching** using [Neon](https://neon.tech/) and GitHub Actions, allowing you to safely test schema changes and operations in isolated environments triggered by pull request events.

## Features

- **PostgreSQL Integration:** Connects to a PostgreSQL database for data storage.
- **Automated Migrations:** Uses `node-pg-migrate` for managing schema changes.
- **Ephemeral Database Branching:** Automatically creates a temporary database branch for each pull request using Neon. Once the pull request is closed, the temporary branch is deleted.
- **Simple Todo Management:** Basic CRUD operations for managing todo items.

## Prerequisites

- Node.js (v14 or higher)
- PostgreSQL database server
- npm (Node Package Manager)

## Project Structure

```
├── migrations/
│   └── 1698765432345_create-todos-table.js
├── .env.example
├── .env
├── index.js
├── package.json
└── README.md
```

## Setup

1. Clone the repository
2. Install dependencies:
   ```bash
   npm install
   ```
3. Create a `.env` file based on `.env.example`:
   ```bash
   cp .env.example .env
   ```
4. Update the `.env` file with your PostgreSQL connection details:
   ```
   DATABASE_URL=postgres://username:password@localhost:5432/database_name
   ```
   If using Neon for ephemeral branches, ensure your Neon project ID and API key are set as GitHub Secrets and/or environment variables.

## Automated Ephemeral Database Branching
This project leverages GitHub Actions and Neon to automate database branching:
- ### How It Works:
   -  When a pull request targeting the main branch is created or updated, the workflow triggers.
   -  A new ephemeral database branch (clone) is created on Neon using your managed PostgreSQL instance.
   - Database migrations and tests run against this temporary copy.
   - When the pull request is closed, the workflow deletes the ephemeral branch, cleaning up resources automatically.
- ### Workflow Highlights:
   -  Uses the `neondatabase/create-branch-action` to spin up a database branch.
   -  Uses `node-pg-migrate` to run migrations (migrate:up and migrate:down commands).
   - Automatically deletes the branch using `neondatabase/delete-branch-action` when the pull request closes.


### Available Migration Commands

- Run all pending migrations:
  ```bash
  npm run migrate:up
  ```
- Revert the last migration:
  ```bash
  npm run migrate:down
  ```

### Current Migrations

1. `1698765432345_create-todos-table.js`
   - Creates the initial `todos` table with:
     - `id` (Primary Key)
     - `title` (varchar)
     - `completed` (boolean)
     - `created_at` (timestamp) (added later through pull request)

## Running the Application

Start the application:
```bash
npm start
```

The application will:
1. Connect to the PostgreSQL database (or Neon ephemeral branch, if triggered by a pull request).
2. Display the current timestamp.
3. List all todos in the database.

## Environment Variables

| Variable      | Description                | Example                                           |
|--------------|----------------------------|---------------------------------------------------|
| DATABASE_URL | PostgreSQL connection URL  | postgres://username:password@localhost:5432/todos |

## Security Notes

- Do not commit `.env` file to version control.
- Ensure that sensitive data (such as database credentials) is stored securely.
- The `.env.example` file should not contain actual credentials.

## Scripts

- `npm start` - Run the application
- `npm run migrate` - Run database migrations
- `npm run migrate:up` - Apply pending migrations
- `npm run migrate:down` - Revert the last migration

## Dependencies

- `pg` - PostgreSQL client for Node.js
- `node-pg-migrate` - Database migration tool
- `dotenv` - Environment variable management

## Conclusion

By integrating automated ephemeral database branching, this project not only demonstrates standard PostgreSQL and migration practices but also shows how to incorporate modern CI/CD techniques to manage database changes safely in a collaborative environment.