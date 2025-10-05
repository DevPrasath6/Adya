# Smart Laundry System — Advanced Developer Guide

This repository implements a microservices-based platform for a smart laundry solution. It contains the web and mobile clients, multiple backend services, ML/AI experiments, digital twin assets, infrastructure as code, and helpers for CI/CD and local development.

Goals of this README
- Provide a clear architecture overview and development workflow.
- Document how to run and test the main components locally.
- Outline CI/CD and release recommendations.
- Provide ML reproducibility and model management guidance.

Contents
- Architecture overview
- Service matrix
- Local development (frontend, backend, infra)
- Testing strategy
- CI/CD and release flow
- ML/AI reproducibility
- Troubleshooting & maintenance notes
- Contribution and governance

----

## Architecture overview

High level (ASCII diagram):

```
+----------------------+          +----------------------+          +----------------+
|      Frontend (W)    | <------> |      API Gateway     | <------> | Auth Service   |
|  (Vite + React TS)   |          | (edge routing, auth) |          +----------------+
+----------------------+          +----------------------+                 ^
			 ^    ^                          ^      ^    ^                      (JWT/OAuth)
			 |    |                          |      |    |                        |
			 |    +--------------------------+      |    +---> Notification svc
			 |                                         |
			 |                                         +--> Laundry Service (core business rules)
			 +----------------------------------------->  Lost&Found svc (ai vision)
																									Payment svc (blockchain)
																									Reporting svc

```

Notes
- Each backend service is self-contained and can be run in a container.
- `api-gateway` aggregates and proxies requests, handles routing and auth integration.
- ML models live under `ai-ml/` and `backend/lostfound-service/ai-vision` and are run/trained with Python tooling.
- Infrastructure as code (k8s / terraform) is under `infra/`.

----

## Service matrix

Provide this table in the repo (or transfer to docs) describing each component:

- frontend/web — Vite + React + TypeScript, Tailwind, shadcn UI (entry: `frontend/web/package.json`).
- frontend/mobile — mobile client (check folder for platform; placeholder package.json exists).
- backend/api-gateway — request routing and aggregation (Dockerfile present).
- backend/auth-service — auth endpoints and token management.
- backend/laundry-service — core domain logic for laundry bookings and lifecycle.
- backend/lostfound-service — lost-and-found with AI-vision inference scripts.
- backend/notification-service — pushes notifications (email/SMS/push).
- backend/payment-service — payment flows and blockchain/hardhat contracts.
- backend/reporting-service — analytics and reporting endpoints.

----

## Local development

Prerequisites
- Node.js >= 18, npm or pnpm/yarn
- Docker & Docker Compose (for local stacks)
- Python 3.10+ for ML tooling (optional unless working on ML)

Recommended workflow
1. Frontend quick dev

```powershell
cd frontend\web
npm ci
npm run dev
# Open http://localhost:5173
```

If you need the backend APIs available, either start individual services or use Docker Compose.

2. Start backend service (individual)

```powershell
cd backend\auth-service
npm ci
npm run dev # or npm start depending on the service scripts
```

3. Start a dev stack with Docker Compose (recommended for integration)

```powershell
cd infra
docker-compose up --build
```

Tips
- Use `docker-compose -f docker-compose.yml -f docker-compose.override.yml` for local overrides (we can add an override to map volumes and enable fast dev).
- If services have missing `package.json` or dev scripts, see the `TODOs` section — we can standardize them.

----

## Environment variables

Keep secrets out of Git. Example per-service `.env.example` files should live next to each service. At the repo root you can maintain a high-level `.env.example` for common variables.

Example minimal variables (root `.env.example` created):

- POSTGRES_URL=postgres://user:pass@db:5432/adya
- REDIS_URL=redis://redis:6379
- JWT_SECRET=replace_with_a_secure_secret

----

## Testing strategy

- Unit tests: co-located with services (run `npm test` inside service folder, or `pytest` for Python projects).
- Integration tests: use `tests/integration` and bring up a dev stack (docker-compose) in CI to run them.
- Load tests: use `tests/load` scripts (e.g., k6 or Locust) against a deployed dev environment.

CI should run unit tests and linters for each project, and run a smaller integration smoke test suite on PRs.

----

## CI/CD recommendations

Start simple and iterate:

1. Pull-request workflow (fast): lint, typecheck, unit tests for changed projects.
2. Merge to main: build artifacts and Docker images, run integration tests, publish images to registry (GHCR/ECR), and deploy to a staging cluster.
3. Release to production: run smoke tests, run DB migrations (with backups), and deploy.

Suggested tools
- GitHub Actions for CI
- Build matrix per service using local `package.json` scripts
- Image registry: GitHub Container Registry or DockerHub
- Deployment: Helm charts or k8s manifests in `infra/k8s`

----

## ML/AI reproducibility

Guidelines
- Pin Python dependencies in `requirements.txt` or `pyproject.toml` in each `ai-ml` subproject.
- Containerize training runs with a `Dockerfile` so experiments are reproducible.
- Version models and datasets with DVC or MLflow. Store artifacts in an S3-compatible bucket.
- Add CI checks for notebooks (nbval) or convert to tests.

Example Dockerfile snippet for training

```
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "train.py"]
```

----

## Troubleshooting

- If your frontend can't reach the backend, verify `API_BASE_URL` env var and that the gateway is running.
- For CORS issues, check gateway and backend service CORS configuration.
- If Docker Compose fails, run `docker-compose down -v` to clear volumes and try again.

----

## Maintenance & governance

- Add Dependabot or Renovate to automate dependency updates.
- Add a security scan step (Trivy/Clair) in CI to scan built images.
- Add release automation (semantic-release) to tag and publish changes.

----

## TODOs (short term)

1. Standardize `package.json` in services missing scripts (`frontend/mobile`, `backend/api-gateway`).
2. Add `infra/docker-compose.override.yml` for local dev (map volumes, dev ports).
3. Add GitHub Actions for frontend and backend CI.
4. Add `.gitattributes` to normalize EOLs (CRLF vs LF) and optionally run a one-time normalization.
5. Add per-service README with run/build/test commands.

----

## Contributing

Please open an issue or PR. Follow the repository coding standards and include tests for new features. Add yourself to `MAINTAINERS.md` if you'll be responsible for a service.

----

If you want, I will now:
- add a `.gitattributes` to normalize line endings and re-normalize the repository, or
- add a GitHub Actions workflow for `frontend/web` CI, or
- create `infra/docker-compose.override.yml` and per-service `.env.example` files to improve local dev.

Tell me which you want next and I'll implement it.
