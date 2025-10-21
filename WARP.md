# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a Node.js Express API for an acquisitions management system. It uses modern JavaScript (ES modules), PostgreSQL with Drizzle ORM, and follows a clean architecture pattern with proper separation of concerns.

## Development Commands

### Core Development

- `npm run dev` - Start development server with file watching
- `npm run lint` - Run ESLint to check code quality
- `npm run lint:fix` - Automatically fix linting issues
- `npm run format` - Format code with Prettier
- `npm run format:check` - Check code formatting without making changes

### Database Operations

- `npm run db:generate` - Generate database migrations with Drizzle Kit
- `npm run db:migrate` - Apply database migrations
- `npm run db:studio` - Open Drizzle Studio for database GUI

## Architecture Overview

### Directory Structure

The application follows a layered architecture pattern:

```
src/
├── config/         # Configuration files (database, logger)
├── controllers/    # Request handlers and business logic entry points
├── middleware/     # Express middleware functions
├── models/         # Drizzle ORM schema definitions
├── routes/         # API route definitions
├── services/       # Business logic and data access layer
├── utils/          # Utility functions (JWT, cookies, formatting)
├── validations/    # Zod schema validations
├── app.js          # Express application setup
├── index.js        # Application entry point
└── server.js       # Server startup configuration
```

### Key Technologies

- **Runtime**: Node.js with ES modules (`"type": "module"`)
- **Framework**: Express.js 5.x
- **Database**: PostgreSQL via Neon Database
- **ORM**: Drizzle ORM with Drizzle Kit for migrations
- **Validation**: Zod for request validation
- **Authentication**: JWT with bcrypt for password hashing
- **Logging**: Winston with file and console transports
- **Code Quality**: ESLint + Prettier with specific configurations

### Import Path Mapping

The project uses Node.js import maps for clean imports:

- `#config/*` → `./src/config/*`
- `#controllers/*` → `./src/controllers/*`
- `#middleware/*` → `./src/middleware/*`
- `#models/*` → `./src/models/*`
- `#routes/*` → `./src/routes/*`
- `#services/*` → `./src/services/*`
- `#utils/*` → `./src/utils/*`
- `#validations/*` → `./src/validations/*`

Always use these mapped imports instead of relative paths.

### Database Layer

- **Connection**: Uses Neon serverless PostgreSQL with connection pooling
- **Schema**: Defined in `src/models/` using Drizzle's pgTable
- **Migrations**: Generated in `./drizzle/` directory
- **Configuration**: Database URL from environment variable `DATABASE_URL`

### Authentication Flow

- JWT-based authentication with HTTP-only cookies
- Password hashing with bcrypt (salt rounds: 10)
- Cookie configuration varies by environment (secure flag for production)
- Token expiration: 1 day
- Cookie maxAge: 15 minutes

### Logging Strategy

- Winston logger with JSON format for structured logging
- File logging: `logs/error.log` (errors only) and `logs/combined.log` (all levels)
- Console logging in development with colorized output
- Log level configurable via `LOG_LEVEL` environment variable

### Validation Approach

- Zod schemas for request validation in `src/validations/`
- Centralized validation error formatting
- Validation happens at controller level before service calls

## Code Style Guidelines

### ESLint Configuration

- 2-space indentation with switch case indentation
- Single quotes for strings
- Semicolons required
- Unix line endings
- Prefer const over let, no var allowed
- Arrow functions preferred over function declarations
- Unused variables allowed if prefixed with underscore

### Prettier Configuration

- Single quotes, semicolons required
- 80 character line width
- 2-space tabs, no tab characters
- Trailing commas in ES5 contexts
- Arrow function parentheses avoided when possible

## Environment Configuration

Required environment variables:

- `DATABASE_URL` - PostgreSQL connection string
- `JWT_SECRET` - Secret key for JWT signing (change in production)
- `PORT` - Server port (defaults to 3000)
- `NODE_ENV` - Environment mode (affects logging and security)
- `LOG_LEVEL` - Logging verbosity (defaults to 'info')

## API Endpoints

Current implemented endpoints:

- `GET /` - Root endpoint with health message
- `GET /health` - Health check with uptime
- `GET /api` - API status check
- `POST /api/auth/sign-up` - User registration (fully implemented)
- `POST /api/auth/sign-in` - User login (placeholder)
- `POST /api/auth/sign-out` - User logout (placeholder)

## Development Workflow

1. Always run `npm run lint` and `npm run format:check` before committing
2. Use `npm run db:generate` after modifying database schemas
3. Apply migrations with `npm run db:migrate` after generation
4. Monitor logs in `logs/` directory during development
5. Use Drizzle Studio (`npm run db:studio`) for database inspection

## Error Handling Patterns

- Controllers catch and format validation errors using Zod
- Services throw descriptive errors that controllers can handle
- Global error handling should be implemented via Express middleware
- Winston logger used for error logging with stack traces in development
