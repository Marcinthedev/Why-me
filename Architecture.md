# Architecture

## AWS Infrastructure

```
GitHub Actions
     │
     │ docker push :latest / :<sha>
     ▼
Amazon ECR (why-me)
     │
     │ auto-deploy on :latest change
     ▼
AWS App Runner
  - public HTTPS endpoint
  - 0.25 vCPU / 0.5 GB RAM
  - health check: GET /
  - env: PORT, NODE_ENV=production
```

## CI/CD

**CI** — runs on every push / PR to `main`
- lint → unit tests → build

**CD** — runs on merge to `main`
- unit tests → docker build → push to ECR (`:latest` + `:<sha>`) → App Runner auto-deploys

Authentication to AWS uses **OIDC** (no long-lived credentials). GitHub secret required: `AWS_ROLE_ARN`.

## App structure

```
src/
├── main.ts              # bootstrap, ValidationPipe, port
├── app.module.ts        # root module
├── app.controller.ts    # route handlers
└── app.service.ts       # business logic
```