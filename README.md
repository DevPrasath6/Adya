# Smart Laundry System

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Repository Structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Testing](#testing)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [License](#license)

## Overview

The Smart Laundry System is an enterprise-grade microservices platform designed to manage laundry operations through web and mobile interfaces. The system integrates machine learning capabilities, blockchain-based payments, and IoT device management to provide a comprehensive solution for modern laundry facilities.

### Key Features

- **Web Dashboard**: React-based administrative interface
- **Mobile Application**: Cross-platform mobile client
- **Microservices Architecture**: Scalable backend services
- **AI/ML Integration**: Demand forecasting and lost item matching
- **Blockchain Payments**: Secure payment processing
- **IoT Integration**: Device monitoring and management
- **Digital Twin**: Azure Digital Twins with Unity simulation

## Architecture

The system follows a microservices architecture pattern with the following components:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Web Client    │    │  Mobile Client  │    │   IoT Devices   │
│  (React + TS)   │    │                 │    │                 │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                    ┌─────────────┴───────────┐
                    │     API Gateway         │
                    │  (Authentication &      │
                    │   Request Routing)      │
                    └─────────────┬───────────┘
                                  │
          ┌───────────────────────┼───────────────────────┐
          │                       │                       │
    ┌─────┴─────┐         ┌───────┴───────┐       ┌───────┴───────┐
    │   Auth    │         │   Laundry     │       │ Notification  │
    │ Service   │         │   Service     │       │   Service     │
    └───────────┘         └───────────────┘       └───────────────┘
          │                       │                       │
    ┌─────┴─────┐         ┌───────┴───────┐       ┌───────┴───────┐
    │ Payment   │         │ Lost & Found  │       │  Reporting    │
    │ Service   │         │   Service     │       │   Service     │
    └───────────┘         └───────────────┘       └───────────────┘
```

## Repository Structure

```
├── frontend/
│   ├── web/                 # React web application
│   └── mobile/              # Mobile application
├── backend/
│   ├── api-gateway/         # API gateway service
│   ├── auth-service/        # Authentication service
│   ├── laundry-service/     # Core business logic
│   ├── lostfound-service/   # Lost & found with AI vision
│   ├── notification-service/# Notification handling
│   ├── payment-service/     # Payment processing
│   └── reporting-service/   # Analytics and reporting
├── ai-ml/
│   ├── federated-learning/  # Distributed learning models
│   ├── laundry-demand-forecast/ # Demand prediction
│   ├── lostfound-matching/  # Item matching algorithms
│   └── notebooks/           # Jupyter notebooks
├── digital-twin/
│   ├── azure-digital-twins/ # Azure DT configurations
│   ├── configs/             # Digital twin configs
│   └── unity-simulation/    # Unity simulation assets
├── iot/
│   ├── edge/                # Edge computing components
│   ├── firmware/            # Device firmware
│   └── mqtt-broker/         # MQTT broker configuration
├── infra/
│   ├── docker-compose.yml   # Local development stack
│   ├── k8s/                 # Kubernetes manifests
│   └── terraform/           # Infrastructure as code
├── tests/
│   ├── unit/                # Unit tests
│   ├── integration/         # Integration tests
│   └── load/                # Performance tests
└── scripts/                 # Deployment and utility scripts
```

## Prerequisites

- **Node.js**: Version 18 or higher
- **npm**: Version 8 or higher (or equivalent package manager)
- **Docker**: Version 20.10 or higher
- **Docker Compose**: Version 2.0 or higher
- **Python**: Version 3.10 or higher (for ML components)

## Getting Started

### Quick Start - Web Application

1. Navigate to the web application directory:
   ```bash
   cd frontend/web
   ```

2. Install dependencies:
   ```bash
   npm ci
   ```

3. Start the development server:
   ```bash
   npm run dev
   ```

4. Access the application at `http://localhost:5173`

### Backend Services

#### Individual Service Development

To run a specific backend service:

```bash
cd backend/auth-service
npm ci
npm run dev
```

#### Full Stack Development

To run the complete development environment:

```bash
cd infra
docker-compose up --build
```

For local development with live reload:

```bash
docker-compose -f docker-compose.yml -f docker-compose.override.yml up
```

## Development Workflow

### Environment Configuration

1. Copy environment templates:
   ```bash
   cp .env.example .env
   ```

2. Configure environment variables according to your development setup.

### Code Standards

- **Frontend**: ESLint and Prettier configuration
- **Backend**: Service-specific linting rules
- **Python/ML**: PEP 8 compliance with Black formatter

### Version Control

- Feature branches should be created from `main`
- Pull requests require code review and passing CI checks
- Commit messages should follow conventional commit standards

## Testing

### Unit Tests

Execute unit tests for individual services:

```bash
# Frontend
cd frontend/web && npm test

# Backend service
cd backend/auth-service && npm test

# Python/ML
cd ai-ml/laundry-demand-forecast && pytest
```

### Integration Tests

Run integration tests against the full stack:

```bash
cd tests/integration
npm test
```

### Load Testing

Performance testing using k6:

```bash
cd tests/load
k6 run performance-test.js
```

## Deployment

### Development Environment

```bash
cd infra
docker-compose up -d
```

### Staging/Production

1. **Container Registry**: Images are built and pushed to GitHub Container Registry
2. **Orchestration**: Kubernetes manifests in `infra/k8s/`
3. **Infrastructure**: Terraform configurations in `infra/terraform/`

### CI/CD Pipeline

The project uses GitHub Actions for:
- Automated testing on pull requests
- Docker image building and publishing
- Deployment to staging environments
- Security scanning and dependency checks

## Contributing

### Development Setup

1. Fork the repository
2. Clone your fork locally
3. Create a feature branch
4. Make your changes with appropriate tests
5. Submit a pull request

### Code Review Process

- All changes require review from project maintainers
- Automated checks must pass before merge
- Documentation updates required for new features

### Issue Reporting

- Use GitHub Issues for bug reports and feature requests
- Include relevant logs and environment information
- Follow the provided issue templates

## License

This project is licensed under the terms specified in the `LICENSE` file located in the project root directory.

---

**Note**: For detailed service-specific documentation, refer to the README files in individual service directories.
