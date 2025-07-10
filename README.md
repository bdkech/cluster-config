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

### 2. Bootstrap with the App of Apps Pattern

1. **Apply the parent Application manifest:**
   ```sh
   kubectl apply -f argocd/applications/app-of-apps.yaml
   ```
2. **Argo CD will automatically discover and manage all child Applications in `argocd/applications/`** (such as Traefik, MetalLB, etc).

### 3. Managing Namespaces

- All required namespaces are managed declaratively via the `cluster-namespaces` Application. No manual namespace creation is needed.

### 4. Managing Gateway API Resources

- Define your Gateway and HTTPRoute resources in the `manifests/` directory and sync them via Argo CD.

## Directory Structure

- `argocd/applications/` — Argo CD Application manifests (parent and children)
- `manifests/` — Cluster-wide manifests (namespaces, Gateway API resources, etc.)
- `README.md` — This file

## Notes
- The App of Apps pattern enables scalable, declarative management of all cluster applications from a single parent Application.
- Traefik is the ingress controller, deployed via Helm with Gateway API enabled.
- MetalLB provides LoadBalancer IPs for bare metal clusters and is managed via Argo CD and Helm.
- All namespaces are managed as a single Application (`cluster-namespaces`).
