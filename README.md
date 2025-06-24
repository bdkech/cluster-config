# cluster-config

This directory contains the Kubernetes configuration for the cluster, including manifests, Helm charts, and GitOps setup. It is intended to be the source of truth for the cluster's desired state and should be managed via version control and automated CI/CD pipelines.

## Structure

- `base/` - Base manifests (namespaces, storage classes, etc.)
- `dashboard/` - Kubernetes Dashboard manifests or Helm chart
- `argo-workflows/` - Argo Workflows manifests or Helm chart
- `user-management/` - RBAC, OIDC config, user roles, etc.
- `monitoring/` - (Optional) Monitoring stack (Prometheus, Grafana, etc.)
- `overlays/` - Environment-specific overlays (dev, prod, etc.)

Each subdirectory should contain a `README.md` with details about its contents and usage. 