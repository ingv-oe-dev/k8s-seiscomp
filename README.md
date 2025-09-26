# Kubernetes Manifests for Seiscomp

This repository contains the Kubernetes manifests for deploying the [Seiscomp](https://www.seiscomp.de/) software suite. The manifests are managed using **Kustomize** to support multiple environments (e.g., `staging`, `production`) in a clean and declarative way.

## Prerequisites 🔧

Before you begin, ensure you have the following tools installed:
* `kubectl`: For interacting with the Kubernetes cluster.
* `kustomize`: For building the manifests. (Often included with `kubectl` v1.14+).
* Access to a configured Kubernetes cluster.

---

## Directory Structure 📁

The project is divided into logical components, each with its own `base` configuration and corresponding `overlays`.

```
.
├── manifests
│   ├── database      # Manifests for the database (StatefulSet, Service, etc.)
│   │   ├── base
│   │   └── overlays
│   │       ├── production
│   │       └── staging
│   └── seiscomp      # Manifests for the Seiscomp application (Deployment, Service, etc.)
│       ├── base
│       └── overlays
│           ├── production
│           └── staging
└── README.md
```

* **`base`**: Contains the common manifests and default configurations that do not change between environments.
* **`overlays`**: Contains environment-specific customizations (`staging`, `production`). These include patches for modifying replicas, resources, hostnames, and other variables.

---

## Configuration Management

Sensitive and environment-specific configuration (e.g., database credentials, API keys) is managed via `.env` and `.env.secret` files, which **must not be committed to Git**.

Kustomize uses `configMapGenerator` and `secretGenerator` to create `ConfigMap` and `Secret` resources from these files.

**Action Required**: Before deploying, you must create the necessary files in the `base` directory of each component.

**Example `.gitignore`**:
Make sure to add these files to your `.gitignore` to avoid exposing sensitive data.

```gitignore
# Ignore environment-specific configuration files
manifests/**/dot_env
manifests/**/dot_env.secret
```

---

## How to Deploy 🚀

To apply the manifests to a cluster, use `kubectl apply -k` and point to the desired overlay directory.

### Deploying to Staging

```bash
# 1. Apply the database
kubectl apply -k manifests/database/overlays/staging

# 2. Apply the Seiscomp application
kubectl apply -k manifests/seiscomp/overlays/staging
```

### Deploying to Production

```bash
# 1. Apply the database
kubectl apply -k manifests/database/overlays/production

# 2. Apply the Seiscomp application
kubectl apply -k manifests/seiscomp/overlays/production
```

### Validating the Configuration (Dry-Run)

To view the final YAML output without applying it to the cluster, use `kustomize build`. This is very useful for debugging.

```bash
# Example: view the production configuration for Seiscomp
kustomize build manifests/seiscomp/overlays/production
```