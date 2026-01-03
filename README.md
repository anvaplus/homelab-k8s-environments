# homelab-k8s-environments

## Overview

This repository contains version declarations for all applications deployed in the Homelab environment. It acts as the **version registry** in the GitOps workflow, tracking which version of each application is deployed in each environment.

*Note: This document represents a plan. The contents of this repository will evolve as the homelab is built and refined.*

## 🔗 Related Repositories

This repository is part of a 4-repository GitOps architecture:

- **[homelab-k8s-argo-config](https://github.com/anvaplus/homelab-k8s-argo-config)** - ArgoCD configurations for platform tools
- **[homelab-k8s-base-manifests](https://github.com/anvaplus/homelab-k8s-base-manifests)** - Helm chart templates for applications
- **[homelab-k8s-environments](https://github.com/anvaplus/homelab-k8s-environments)** (This repository) - Version declarations for applications
- **[homelab-k8s-environments-apps](https://github.com/anvaplus/homelab-k8s-environments-apps)** - Application configuration values

## Purpose

This repository:
- **Tracks application versions** deployed in each environment (DEV and PROD).
- **Enables automated deployments** for the development environment.
- **Provides a version history** through Git commit history.
- **Facilitates version promotion** from DEV to PROD.
- **Stores secrets** in a secure way.

## Repository Structure

The planned structure for this repository is as follows:
```
└── homelab/
    ├── dev/                      # Development environment versions
    │   ├── integration-layer/    # Integration applications
    │   │   └── integration-app1/
    │   │       └── values.yaml
    │   └── documents/            # Document handling applications
    │       └── document-app1/
    │           └── values.yaml
    └── prod/                     # Production environment versions
        └── (same structure as dev)
```

## How It Works

### Version Management

Each application has a `values.yaml` file containing its version:

```yaml
# Application version (Docker image tag)
version: v1.2.3
```

### Automated Updates (DEV Environment)

For the **development environment**, this repository can be automatically updated by a CI/CD pipeline:

1. A new version of an application is built and a Docker image is pushed.
2. The CI/CD pipeline automatically updates the `version` in the corresponding `values.yaml` file in the `dev` directory.
3. The change is pushed to the `main` branch.
4. ArgoCD detects the change and deploys the new version to the DEV environment.

### Manual Promotion (PROD Environment)

Promoting an application to the **production environment** is a manual process:

1. Verify the application is stable in the DEV environment.
2. Create a pull request to update the version in the `prod` directory.
3. After review and approval, the PR is merged.
4. ArgoCD detects the change and deploys the new version to the PROD environment.

This controlled promotion ensures stability in the production environment.

## Integration with Other Repositories

This repository provides the `version` value to the Helm charts defined in `homelab-k8s-base-manifests`. ArgoCD combines the chart, the version from this repo, and the configuration from `homelab-k8s-environments-apps` to deploy the application.
