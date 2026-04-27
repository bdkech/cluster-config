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

### 5. DNS & Router Setup

For external access, configure once at your registrar and home router:

| What | Setting |
|------|---------|
| `*.scudco.com` DNS A record | `68.44.181.115` (home ISP IP) |
| Router port-forward TCP 80 | → `10.0.1.200` (Traefik) |

Knative services are available at `<service>.<namespace>.scudco.com`. Traefik routes all inbound traffic on `10.0.1.200` and proxies Knative requests to Kourier internally.

### 6. Deploying Knative Services

1. **Deploy a Knative Service:**
   ```sh
   kubectl apply -f test_app/knative-hello-world.yaml
   ```

2. **Access the service:**
   ```sh
   # Get the service URL
   kubectl get ksvc hello-knative

   # Test before DNS propagates (via Host header to Traefik's MetalLB IP):
   curl -H "Host: hello-knative.default.scudco.com" http://10.0.1.200

   # Once DNS is live:
   curl http://hello-knative.default.scudco.com
   ```

### 7. Traefik Dashboard (Local Only)

The dashboard is not exposed externally. Access it via port-forward:
```sh
kubectl port-forward svc/traefik -n traefik 8080:80
# then open: http://dashboard.localhost:8080
```

## Directory Structure

- `argocd/applications/` — Argo CD Application manifests (parent and children)
- `manifests/` — Cluster-wide manifests (namespaces, Gateway API resources, Knative configs)
- `test_app/` — Example applications for testing
- `README.md` — This file

## Installed Components

- **Argo CD** — GitOps continuous delivery tool
- **Traefik** — Ingress controller with Gateway API support
- **MetalLB** — LoadBalancer implementation for bare metal clusters
- **Knative Serving** — Serverless containers platform
- **Gateway API** — Standard for service networking

## Notes
- The App of Apps pattern enables scalable, declarative management of all cluster applications from a single parent Application.
- Traefik is the ingress controller, deployed via Helm with Gateway API enabled.
- MetalLB provides LoadBalancer IPs for bare metal clusters and is managed via Argo CD and Helm.
- Knative services are accessible externally via Traefik and Kourier at `<service>.<namespace>.scudco.com`.
- All namespaces are managed as a single Application (`cluster-namespaces`).
