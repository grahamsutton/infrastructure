# Infrastructure

This repository contains the GitOps configuration for managing Kubernetes infrastructure using ArgoCD. It implements the App of Apps pattern to declaratively manage application deployments across environments.

## Prerequisites

- Access to a Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- For local development: `minikube` installed and running

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

### 4. Login to ArgoCD UI

Go to `localhost:8080`.

The default password is injected as a secret (which should be deleted after a new password has been set), which can be accessed by doing:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
```

Copy the base64 encoded password value and do:

```bash
echo <copied-password> | base64 --decode
```

Copy the value without the trailing `%` and use it as the password.

The ArgoCD UI will be available at: https://localhost:8080

## Repository Structure

- `app-of-apps.yaml` - Root ArgoCD application that manages all other applications
- `apps/` - Individual application configurations managed by ArgoCD

## Local Development Access

When developing locally with minikube, services are not directly accessible via NodePort on macOS with the Docker driver. Use the following approach to access services:

### Accessing Services via Ingress (Recommended)

1. **Enable minikube tunnel** (run in a separate terminal and keep running):
   ```bash
   minikube tunnel
   ```

2. **Update your hosts file** (`/etc/hosts`) to point services to localhost:
   ```bash
   echo "127.0.0.1 argocd.operion.local" | sudo tee -a /etc/hosts
   ```

3. **Access services directly**:
   - ArgoCD UI: `http://argocd.operion.local`
   - Other services: `http://<service-name>.operion.local`

### Alternative: Port Forwarding

If you prefer not to use minikube tunnel, you can use port forwarding:

```bash
# ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Ingress Controller (for debugging)
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8080:80
```

**Note**: With minikube tunnel, you get the full ingress experience and can access multiple services using their configured hostnames.

For detailed ArgoCD documentation, visit: https://argo-cd.readthedocs.io/en/stable/
