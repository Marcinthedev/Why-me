# Why-me

A NestJS REST API built with TypeScript, deployed to AWS via GitHub Actions and App Runner.

---

## Prerequisites

Make sure you have the following installed before running anything:

| Tool | Version | Purpose |
|------|---------|---------|
| [Node.js](https://nodejs.org) | 20+ | JavaScript runtime |
| [npm](https://www.npmjs.com) | 9+ | Package manager (comes with Node) |
| [Docker](https://www.docker.com) | any recent | Running containerized builds |
| [AWS CLI](https://aws.amazon.com/cli/) | v2 | Interacting with AWS (deploy only) |

Check your versions:

```bash
node --version   # should print v20.x.x or higher
npm --version    # should print 9.x.x or higher
docker --version # should print Docker version 24.x.x or higher
```

---

## Running locally

### 1. Install dependencies

```bash
npm install
```

This reads `package.json` and downloads all packages into `node_modules/`.

### 2. Start in development mode (recommended)

```bash
npm run start:dev
```

The server starts on [http://localhost:3000](http://localhost:3000) and **automatically restarts** whenever you save a file. This is the mode you'll use day-to-day while building features.

### 3. Start in debug mode

```bash
npm run start:debug
```

Same as dev mode but also opens Node's debugger on port `9229`. Attach VS Code's debugger by pressing `F5` or using the "Attach to Node" launch configuration.

### 4. Start in production mode

```bash
npm run build       # compile TypeScript → dist/
npm run start:prod  # run compiled output
```

This is how the app runs in production. The `build` command writes compiled JavaScript to `dist/`. The `start:prod` command then runs `node dist/main` directly — no TypeScript compiler overhead.

### Environment variables

The app reads these from the environment (or falls back to defaults):

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | `3000` | Port the HTTP server listens on |

Set them inline or in a `.env` file (not committed to git):

```bash
PORT=8080 npm run start:dev
```

Or create a `.env` file:

```
PORT=8080
```

---

## Running with Docker

Use this to run the app exactly as it runs in production, on your local machine.

### Build the image

```bash
docker build -t why-me .
```

This runs a multi-stage build — dependencies are installed and TypeScript is compiled inside Docker, and only the production output is kept in the final image. See [Architecture.md](Architecture.md) for a detailed explanation.

### Run the container

```bash
docker run -p 3000:3000 why-me
```

The app is now accessible at [http://localhost:3000](http://localhost:3000).

### Run with a custom port

```bash
docker run -p 8080:8080 -e PORT=8080 why-me
```

### Stop the container

```bash
docker ps                         # find the container ID
docker stop <container-id>
```

---

## Testing

### Unit tests

```bash
npm test
```

Runs all `*.spec.ts` files in `src/`. These test individual classes in isolation (no HTTP, no database).

### Unit tests in watch mode

```bash
npm run test:watch
```

Re-runs affected tests whenever you save a file. Use this while writing tests.

### End-to-end (E2E) tests

```bash
npm run test:e2e
```

Boots the full NestJS application and sends real HTTP requests against it using [supertest](https://github.com/ladjs/supertest). These live in `test/*.e2e-spec.ts`.

### Test coverage report

```bash
npm run test:cov
```

Runs unit tests and generates a coverage report in `coverage/`. Open `coverage/lcov-report/index.html` in your browser to explore which lines are covered.

---

## Linting and formatting

### Check for lint errors

```bash
npm run lint
```

Runs ESLint across all TypeScript files in `src/` and `test/`. The `--fix` flag is included, so auto-fixable issues (e.g. missing semicolons) are corrected in-place.

### Format all files

```bash
npm run format
```

Runs Prettier across all TypeScript files. Prettier enforces consistent style (indentation, quotes, trailing commas) without requiring decisions.

---

## Project structure

```
why-me/
├── src/
│   ├── main.ts                 # Entry point — boots the app
│   ├── app.module.ts           # Root module — wires everything together
│   ├── app.controller.ts       # HTTP route handlers
│   ├── app.controller.spec.ts  # Unit tests for the controller
│   └── app.service.ts          # Business logic
├── test/
│   ├── app.e2e-spec.ts         # End-to-end HTTP tests
│   └── jest-e2e.json           # Jest config for E2E
├── dist/                       # Compiled output (git-ignored)
├── .github/
│   └── workflows/
│       ├── ci.yml              # Lint + test on every push/PR
│       └── deploy.yml          # Build + push to ECR on merge to main
├── Dockerfile                  # Multi-stage container build
├── tsconfig.json               # TypeScript compiler options
├── nest-cli.json               # NestJS CLI configuration
└── package.json                # Dependencies and npm scripts
```

---

## Available scripts

| Command | What it does |
|---------|-------------|
| `npm run start` | Start using NestJS CLI (development) |
| `npm run start:dev` | Start with file watching |
| `npm run start:debug` | Start with debugger on port 9229 |
| `npm run start:prod` | Run compiled output from `dist/` |
| `npm run build` | Compile TypeScript to `dist/` |
| `npm test` | Run unit tests once |
| `npm run test:watch` | Run unit tests in watch mode |
| `npm run test:e2e` | Run end-to-end tests |
| `npm run test:cov` | Run unit tests with coverage report |
| `npm run lint` | Run ESLint with auto-fix |
| `npm run format` | Run Prettier formatter |

---

## Deployment

Deployment is fully automated. Every merge to `main`:

1. GitHub Actions runs the CI checks (lint + tests)
2. Builds a Docker image
3. Pushes it to Amazon ECR
4. AWS App Runner detects the new image and deploys it automatically

See [Architecture.md](Architecture.md) for a full explanation of the infrastructure and how App Runner works.

For first-time AWS setup, see the one-time setup steps in [Architecture.md](Architecture.md#one-time-aws-setup).