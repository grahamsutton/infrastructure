# Infrastructure

This repository contains the GitOps configuration for managing Kubernetes infrastructure using ArgoCD. It implements the App of Apps pattern to declaratively manage application deployments across environments.

## Prerequisites

- Access to a Kubernetes cluster
- `kubectl` configured to communicate with your cluster

## Initial Cluster Setup

### 1. Install ArgoCD

Create the ArgoCD namespace and install the core components:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Configure External Access

Patch the ArgoCD server service to enable external access:

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

### 3. Access the ArgoCD UI

Use port forwarding to access the ArgoCD web interface:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

The ArgoCD UI will be available at: https://localhost:8080

## Repository Structure

- `app-of-apps.yaml` - Root ArgoCD application that manages all other applications
- `apps/` - Individual application configurations managed by ArgoCD

For detailed ArgoCD documentation, visit: https://argo-cd.readthedocs.io/en/stable/
