# 🚀 Greeting APIs GitOps Deployment (FluxCD + Kustomize)

This repository manages the GitOps deployment of multiple APIs to a single Kubernetes cluster using [FluxCD](https://fluxcd.io) and [Kustomize](https://kustomize.io).

## 🧭 Overview

This monorepo follows GitOps principles and is structured to:

- Deploy four APIs: **Rails**, **Node.js**, **Go**, and **.NET**
- Use FluxCD to continuously reconcile cluster state from Git
- Apply configurations using Kustomize overlays

## 🏗️ Repository Structure

```
apps/
├── base/
│   ├── dotnet/
│   │   ├── deployment.yaml
│   │   ├── kustomization.yaml
│   │   └── service.yaml
│   ├── go/
│   │   ├── deployment.yaml
│   │   ├── kustomization.yaml
│   │   └── service.yaml
│   ├── nodejs/
│   │   ├── deployment.yaml
│   │   ├── kustomization.yaml
│   │   └── service.yaml
│   └── rails/
│       ├── deployment.yaml
│       ├── kustomization.yaml
│       └── service.yaml
├── dev/
│   ├── dotnet/
│   │   └── kustomization.yaml
│   ├── go/
│   │   └── kustomization.yaml
│   ├── nodejs/
│   │   └── kustomization.yaml
│   └── rails/
│       └── kustomization.yaml
└── image-automation/
    ├── go-imagepolicy.yaml
    └── go-imagerepo.yaml

clusters/
└── dev/
    ├── flux-system/
    │   ├── gotk-components.yaml
    │   ├── gotk-sync.yaml
    │   └── kustomization.yaml
    ├── image-update.yaml
    └── kustomization.yaml

infrastructure/
└── dev/
    ├── ingress.yaml
    ├── kustomization.yaml
    └── namespace.yaml

services/
└── go/
    ├── api.go
    ├── Dockerfile
    └── go.mod
```

## 📦 What's Deployed

Each API is deployed via:

- A `Deployment`
- A `Service`
- An optional `Ingress` or `Secret`

The `rails` API currently embeds `SECRET_KEY_BASE` directly in the deployment. Secrets will be externalized in the future.

## 🚀 Bootstrapping Flux

> Requires: `kubectl`, `flux` CLI, and a working Kubernetes cluster

```bash
flux bootstrap github   --owner=<your-github-username>   --repository=<this-repo-name>   --branch=main   --path=clusters/dev   --personal
```

This installs Flux in the cluster and starts reconciling your `clusters/dev` path.

## 🔁 Continuous Delivery

Once Flux is bootstrapped:

- Changes to files under `apps/` or `clusters/` are automatically applied to your Kubernetes cluster.
- This enables automated and auditable delivery of changes via Git.

## Continuous Integration & Deployment (CI/CD)

This project uses GitHub Actions for CI/CD automation:

- **Testing & Linting:**
  - Runs `go vet` and `staticcheck` to catch code issues and enforce best practices.
- **Security Scanning:**
  - Uses `gosec` to scan the Go codebase for security vulnerabilities.
- **Docker Image Build & Push:**
  - Builds the Docker image for the Go service (`services/go`), then pushes it to Docker Hub at `balenabdalla/go-greeting-api`.
- **Automated Deployment with Flux:**
  - Once the image is pushed, Flux (configured in the `clusters/dev/flux-system` directory) detects the new image and updates the deployment in the Kubernetes cluster automatically.

The workflow file can be found at `.github/workflows/go.yaml`.

---

> Maintained by [Balen Abdalla](https://github.com/Balen-Dev)
