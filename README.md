# Cluster Config

This repository manages the configuration of a Kubernetes cluster using GitOps principles with Argo CD.

## Quick Start

### 1. Manual Installation of Argo CD

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

- Access the Argo CD UI at [https://localhost:8080](https://localhost:8080)
- Login as `admin` with the password above.

### 2. Bootstrapping GitOps-managed Resources

- All cluster resources are managed via Argo CD Applications in the `argocd/applications/` directory.
- Example: To install Contour ingress controller, Argo CD will sync the `contour.yaml` Application manifest, which deploys Contour via Helm.

### 3. Directory Structure

```
argocd/
  applications/
    contour.yaml      # Argo CD Application for Contour
ingress/
  contour/
    helm-values.yaml  # Custom Helm values for Contour
```

### 4. Adding More Applications

- Add new Argo CD Application manifests to `argocd/applications/`.
- Add your app manifests or Helm values under appropriate directories.

---
