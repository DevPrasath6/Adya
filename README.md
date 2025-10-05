# Smart Laundry System

This repository contains the Smart Laundry System — a microservices-based platform with frontend (web & mobile), multiple backend services, ML/AI experiments, and infrastructure-as-code for local and cloud deployment.

This README gives a high-level overview and quick developer setup steps to get the web frontend running and to find the main components of the project.

## Repository layout (top-level)

- `frontend/`
	- `web/` — Vite + React + TypeScript web application (primary web UI)
	- `mobile/` — mobile app (package.json placeholder)
- `backend/` — microservices
	- `api-gateway/` — gateway for routing requests
	- `auth-service/` — authentication
	- `laundry-service/` — core laundry business logic
	- `lostfound-service/` — lost & found with ai-vision subfolder
	- `notification-service/` — notifications
	- `payment-service/` — payments and blockchain contracts
	- `reporting-service/` — reporting and analytics
- `ai-ml/` — ML experiments and training pipelines (federated learning, demand forecasting, matching)
- `digital-twin/` — Azure Digital Twins + Unity simulation assets
- `infra/` — `docker-compose.yml`, `k8s/`, `terraform/` for infra provisioning
- `scripts/` — helper scripts (`db-migrate.sh`, `deploy.sh`, `seed-data.js`)
- `tests/` — unit, integration, load tests
- `docs/` — design docs, SRS, API specs

## Quick start — Web frontend (local dev)

Prerequisites

- Node.js (recommended active LTS, e.g., 18+)
- npm (or yarn/pnpm if you prefer; the `web` project uses npm scripts)

Start dev server for the web app:

```powershell
cd frontend\web
npm ci
npm run dev
```

Open the URL printed by Vite (usually `http://localhost:5173`). The frontend expects backend APIs to be available; for simple UI work you can still use static pages or mock APIs.

## Backend — overview & local dev

Each backend service has its own folder under `backend/` and contains a `Dockerfile`. Many services have `src/`, `routes/`, and `models/` subfolders. To run the backend locally you can either:

- Run services individually (install deps inside the service folder and run):

```powershell
cd backend\auth-service
npm ci
npm start
```

- Or use Docker Compose for a local stack (recommended for integration testing and end-to-end flows):

```powershell
cd infra
docker-compose up --build
```

Note: Some `package.json` files are placeholders or empty (for example `frontend/mobile`, `backend/api-gateway`). See TODOs below.

## ML / AI

ML projects live under `ai-ml/` and `backend/lostfound-service/ai-vision`. These are Python-based (notebooks, training scripts). For reproducibility:

- Add a Python virtual environment and install requirements (look for `requirements.txt` or `pyproject.toml` in the specific ML folder).

Example:

```powershell
cd ai-ml\laundry-demand-forecast
# create venv using your preferred Python version
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## Infrastructure

- `infra/docker-compose.yml` provides local stack definitions.
- `infra/k8s/` and `infra/terraform/` contain manifests and IaC for cloud deployments.

## Testing

The `tests/` folder contains unit, integration, and load test suites. Check each service for its own test runner and add `test` scripts to `package.json` where missing.

## Recommended next steps / TODOs

- Fill in/standardize `package.json` for root and services with missing package manifests (e.g., `frontend/mobile`, `backend/api-gateway`).
- Add a root `package.json` to enable workspace scripts (optional: pnpm/Yarn workspaces).
- Add CI workflows for linting, typechecking, and running unit tests (start with `frontend/web`).
- Add `.env.example` files per service and a root `.env.example` for common vars.
- Add CONTRIBUTING.md and per-service README files describing run/build/test steps.
- For ML: pin python deps, add reproducible Dockerfile for training, and consider DVC/MLflow for model versioning.

## Where to find things

- Frontend web: `frontend/web`
- Mobile app: `frontend/mobile` (placeholder)
- Backend services: `backend/*`
- Infra: `infra/docker-compose.yml`, `infra/k8s`, `infra/terraform`
- ML: `ai-ml/*` and `backend/lostfound-service/ai-vision`

## Who to contact / Contribution

Please add maintainer/contact info here (GitHub usernames or team email).

---

If you'd like, I can now:
- add a GitHub Actions workflow that runs `npm ci`, `npm run lint`, and `npm run build` for `frontend/web`, or
- create `.env.example` plus a `docker-compose.override.yml` for local dev.

Tell me which of the two (CI workflow or env/compose) you'd like next and I'll implement it.
