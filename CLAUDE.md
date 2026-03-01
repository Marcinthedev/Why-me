# CLAUDE.md

This file provides guidance to AI assistants working in this repository.

## Project Overview

**why-me** is a NestJS REST API application bootstrapped from the official NestJS starter template. It is a TypeScript/Node.js server-side application using the Express adapter. The project is at an early stage (v0.0.1) with only the default scaffold in place.

## Tech Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js |
| Framework | NestJS 10 (Express platform) |
| Language | TypeScript 5 |
| Testing | Jest 29 + Supertest (unit & e2e) |
| Linter | ESLint 8 + @typescript-eslint |
| Formatter | Prettier 3 |
| Build | `@nestjs/cli` (`nest build`) |

## Repository Structure

```
why-me/
├── src/                        # Application source code
│   ├── main.ts                 # Entry point — bootstraps the app on port 3000
│   ├── app.module.ts           # Root NestJS module
│   ├── app.controller.ts       # Root controller (GET / → "Hello World!")
│   └── app.service.ts          # Root service (business logic)
├── test/                       # End-to-end tests
│   ├── app.e2e-spec.ts         # E2E spec for the root endpoint
│   └── jest-e2e.json           # Jest config for e2e tests
├── dist/                       # Compiled output (git-ignored)
├── nest-cli.json               # NestJS CLI config (sourceRoot: src, deleteOutDir: true)
├── tsconfig.json               # TypeScript compiler config
├── tsconfig.build.json         # TypeScript config for production builds (excludes tests)
├── .eslintrc.js                # ESLint config
├── .prettierrc                 # Prettier config
└── package.json                # NPM scripts and dependencies
```

## Development Commands

```bash
# Install dependencies
npm install

# Start in development (watch mode — auto-reloads on file changes)
npm run start:dev

# Start normally
npm run start

# Start with debugger
npm run start:debug

# Build for production (output: ./dist)
npm run build

# Start production build
npm run start:prod
```

The server listens on **port 3000** by default (`src/main.ts`).

## Testing

```bash
# Unit tests (Jest, runs *.spec.ts files inside src/)
npm run test

# Unit tests in watch mode
npm run test:watch

# Unit test coverage report (output: ./coverage)
npm run test:cov

# End-to-end tests (runs test/*.e2e-spec.ts)
npm run test:e2e

# Unit tests with Node debugger attached
npm run test:debug
```

- Unit test files live alongside source files in `src/` and use the `*.spec.ts` suffix.
- E2E test files live in `test/` and use the `*.e2e-spec.ts` suffix.

## Linting & Formatting

```bash
# Lint all TypeScript files (auto-fixes fixable issues)
npm run lint

# Format all TypeScript files with Prettier
npm run format
```

**Prettier rules** (`.prettierrc`):
- Single quotes (`singleQuote: true`)
- Trailing commas everywhere (`trailingComma: "all"`)

**ESLint rules** (`.eslintrc.js`):
- Parser: `@typescript-eslint/parser`
- Extends: `plugin:@typescript-eslint/recommended` + `plugin:prettier/recommended`
- Disabled rules: `interface-name-prefix`, `explicit-function-return-type`, `explicit-module-boundary-types`, `no-explicit-any`

## TypeScript Configuration

Key `tsconfig.json` settings to be aware of:

| Setting | Value | Implication |
|---|---|---|
| `target` | `ES2021` | Modern JS output |
| `module` | `commonjs` | CommonJS modules |
| `strictNullChecks` | `false` | Null/undefined not strictly checked |
| `noImplicitAny` | `false` | Implicit `any` types are allowed |
| `emitDecoratorMetadata` | `true` | Required for NestJS DI/decorators |
| `experimentalDecorators` | `true` | Required for NestJS decorators |
| `outDir` | `./dist` | Compiled files go here |

Production builds use `tsconfig.build.json` which extends the base config and excludes `node_modules`, `test`, `dist`, and `**/*spec.ts`.

## NestJS Conventions

This project follows standard NestJS architecture patterns:

- **Modules** (`*.module.ts`): Organise the application into cohesive feature units. Register controllers and providers here.
- **Controllers** (`*.controller.ts`): Handle incoming HTTP requests and return responses. Use decorators like `@Controller()`, `@Get()`, `@Post()`, etc.
- **Services / Providers** (`*.service.ts`): Contain business logic. Injected into controllers via constructor injection.
- **Dependency Injection**: Use `@Injectable()` on services. Inject via constructor parameters typed to the service class.

When adding new features, use the NestJS CLI schematics to generate boilerplate:

```bash
# Generate a new module
npx nest g module <name>

# Generate a new controller
npx nest g controller <name>

# Generate a new service
npx nest g service <name>
```

Generated files are placed in `src/<name>/` following NestJS conventions.

## Git & Branch Workflow

- **Default branch**: `master`
- **Feature branches**: prefix with `claude/` when working from Claude Code (e.g. `claude/<description>-<session-id>`)
- Commit messages should be clear and descriptive
- Run `npm run lint` and `npm run test` before committing

## Important Notes for AI Assistants

- No environment variables or configuration files (`.env`) are present yet; any secrets management should use `@nestjs/config` when added.
- No database, ORM, authentication, or external integrations exist yet — the app is a clean scaffold.
- `node_modules/` and `dist/` are git-ignored; never commit them.
- Keep strict TypeScript settings relaxed (as configured) unless the team explicitly tightens them.
- Prefer editing existing files over creating new ones; follow the one-module-per-feature NestJS pattern.
