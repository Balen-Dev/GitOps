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
│ ├── dotnet/
│ │ ├── deployment.yaml
│ │ └── service.yaml
│ ├── go/
│ │ ├── deployment.yaml
│ │ └── service.yaml
│ ├── nodejs/
│ │ ├── deployment.yaml
│ │ └── service.yaml
│ └── rails/
│ ├── deployment.yaml
│ └── service.yaml
├── dev/
│ ├── dotnet/
│ │ └── kustomization.yaml
│ ├── go/
│ │ └── kustomization.yaml
│ ├── nodejs/
│ │ └── kustomization.yaml
│ └── rails/
│ └── kustomization.yaml

clusters/
└── dev/
└── kustomization.yaml

infrastructure/
└── dev/
├── ingress.yaml
└── kustomization.yaml
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

---

> Maintained by [Balen Abdalla](https://github.com/Balen-Dev)
