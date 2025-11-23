# Module 6 Part 3 - GitOps with ArgoCD

**Student:** Marion Queen Ramos  
**Course:** COMP 4001 - Advanced Development

## What This Does

Automated Kubernetes deployment using GitOps. Push code to GitHub, and it automatically deploys to Kubernetes via ArgoCD.

## Services

- **Backend** - Python Flask API
- **Transactions** - Transaction processing
- **Student Portfolio** - Web portfolio
- **Nginx** - Frontend/reverse proxy
- **MongoDB** - Databases

## Quick Start

### 1. Start Minikube
```bash
minikube start --driver=docker
```

### 2. Install ArgoCD
```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd argo/argo-cd --namespace argocd --create-namespace
```

### 3. Create Secret
```bash
kubectl create secret docker-registry ghcr-creds \
  --namespace default \
  --docker-server=ghcr.io \
  --docker-username="marionqueen" \
  --docker-password="CR_PAT" \
  --docker-email="mramos3@rrc.ca"
```

### 4. Deploy Application
```bash
kubectl apply -f argocd/argocd-application.yaml -n argocd
```

### 5. Access ArgoCD
```bash
# Get password
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d

# Port forward
kubectl port-forward svc/argocd-server -n argocd 8080:80

# Open: http://localhost:8080
# Login: admin / [password from above]
```

## How It Works

1. Push code to GitHub
2. GitHub Actions builds Docker images
3. Workflow updates GitOps repo
4. ArgoCD auto-syncs to Kubernetes
5. Application deployed automatically

## Check Status
```bash
# See all pods
kubectl get pods -n default

# See deployments
kubectl get deployments -n default

# Access application
minikube service nginx
```

## GitHub Secrets Needed

- `CR_PAT` - Container registry token
- `GITOPS_PAT` - GitOps repo token
- `GITOPS_REPO` - GitOps repo name

## Troubleshooting

**ImagePullBackOff?**
- Make GitHub packages public
- Check secret exists: `kubectl get secret ghcr-creds -n default`

**OutOfSync?**
- Click "Sync" in ArgoCD UI
- Check GitHub Actions completed

