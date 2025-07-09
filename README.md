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

### 2. Deploy NGINX Gateway Fabric (Gateway API Ingress)

1. **Create the required namespace:**
   ```sh
   kubectl apply -f manifests/nginx-gateway-namespace.yaml
   ```
2. **Sync the NGINX Gateway Fabric application in Argo CD:**
   - The application manifest is at `argocd/applications/nginx-gateway-fabric.yaml`.
   - This will deploy NGINX Gateway Fabric using the official manifests from the nginxinc GitHub repository.

### 3. Managing Gateway API Resources

- Define your Gateway and HTTPRoute resources in the `manifests/` directory and sync them via Argo CD.

## Directory Structure

- `argocd/applications/` — Argo CD Application manifests
- `manifests/` — Cluster-wide manifests (namespaces, Gateway API resources, etc.)
- `README.md` — This file
