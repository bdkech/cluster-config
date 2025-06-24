# argocd

This directory contains configuration for deploying and managing ArgoCD, the GitOps tool used to synchronize Kubernetes resources from this repository to the cluster.

## Structure

- `install/` - Manifests or Helm values for installing ArgoCD
- `applications/` - ArgoCD Application manifests to manage other parts of the cluster configuration 