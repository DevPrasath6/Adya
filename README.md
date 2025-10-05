# Smart Laundry System

A modular microservices project that supports a web UI, mobile client, backend services, ML components, and infrastructure-as-code for deployment.

This README covers:
- What is in the repository
- How to run the system for development
- Testing, CI/CD and deployment guidance
- ML reproducibility notes
- Maintenance and contribution guidelines

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
## Overview

This repository contains the code and configuration for the Smart Laundry System: a set of backend services, a web frontend, mobile client artifacts, ML experiments, and infrastructure manifests.

Directories you will use most often
- `frontend/web` — web client (Vite + React + TypeScript)
- `backend/*` — individual backend services (each has its own Dockerfile and source)
- `ai-ml` — machine learning experiments, notebooks and training scripts
- `infra` — docker-compose, k8s manifests and terraform code
- `tests` — integration and load tests

## Run the project (developer quick start)

1. Start the web client

```powershell
cd frontend\web
npm ci
npm run dev
# open http://localhost:5173
```

2. Run one backend service (example: auth)

```powershell
cd backend\auth-service
npm ci
npm run dev
```

3. Run the full dev stack

```powershell
cd infra
docker-compose up --build
```

Use `docker-compose -f docker-compose.yml -f docker-compose.override.yml` for local overrides (recommended).

## Testing

- Unit tests: run from each service folder (e.g. `npm test` or `pytest`).
- Integration tests: add CI job that brings up the stack and runs `tests/integration`.
- Load tests: use `tests/load` (k6/Locust) against a staging or dev environment.

## CI/CD

Minimum CI for PRs:
- Lint and typecheck changed projects
- Run unit tests for affected services

On merge to `main`:
- Build and publish Docker images to a registry
- Run the integration smoke suite
- Deploy to staging

Recommended tools: GitHub Actions, GHCR/ECR for images, Helm or k8s manifests for deployment.

## ML workflows

- Pin dependencies in `requirements.txt` or `pyproject.toml`.
- Containerize training jobs and store artifacts in an object store.
- Use DVC/MLflow for model and data versioning.

## Maintenance

- Add Dependabot/ Renovate for dependency updates.
- Add Trivy (or similar) scanning for container images.
- Add `.gitattributes` to normalize line endings across contributors.

## Next steps (recommended)

1. Add GitHub Actions workflow for `frontend/web` CI.
2. Add `infra/docker-compose.override.yml` for local development and per-service `.env.example` files.
3. Standardize `package.json` scripts for backend services that are missing them.

## Support and governance

Open an issue for feature requests or bugs. For operational incidents, create an issue with the `incident` label and ping the maintainers.

Maintainers: update `MAINTAINERS.md` with ownership information.

License: see `LICENSE` at the project root.

---

If you want, I can now implement one of the recommended next steps and push it to the repository. Tell me which one you prefer.
2. Merge to main: build artifacts and Docker images, run integration tests, publish images to registry (GHCR/ECR), and deploy to a staging cluster.
