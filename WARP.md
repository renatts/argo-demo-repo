# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Repository Overview

This is a demo repository for ArgoCD tutorials, containing Kubernetes deployment manifests for GitOps workflows. The repository serves as a source of truth for ArgoCD to sync application deployments to a Kubernetes cluster.

## Repository Structure

- **color-deployment.yaml**: Kubernetes Deployment manifest for a color application
  - Deploys a containerized color service (currently configured for yellow)
  - Uses the image `elevy99927/color:yellow`
  - Configured to run on worker nodes via nodeSelector
- **color-service.yaml**: NodePort Service exposing the color application
- **argocd-application.yaml**: ArgoCD Application manifest for GitOps deployment
- **argo-infra/**: Directory containing ArgoCD infrastructure files
  - `argocd-access-info.txt`: ArgoCD access credentials and URLs
  - `argocd-ingress.yaml`: Secure ingress configuration
  - `argocd-ingress-insecure.yaml`: Insecure ingress configuration

## Common Development Tasks

### Updating Application Deployments

To change the deployed application version or configuration:

```bash
# Edit the deployment manifest
vi color-deployment.yaml

# Commit and push changes for ArgoCD to sync
git add color-deployment.yaml
git commit -m "Update color deployment"
git push origin master
```

### Changing the Color Service Version

To deploy a different color version, modify the image tag in `color-deployment.yaml`:
- Line 19: Change `image: elevy99927/color:yellow` to desired color (e.g., `blue`, `green`, `red`)

### Validating Kubernetes Manifests

```bash
# Validate YAML syntax
kubectl apply --dry-run=client -f color-deployment.yaml
kubectl apply --dry-run=client -f color-service.yaml

# Check manifest structure
kubectl explain deployment.spec
kubectl explain service.spec
```

### ArgoCD CLI Commands

```bash
# Login to ArgoCD
argocd login argocd.local --username admin --password <password> --insecure

# List applications
argocd app list --grpc-web

# Sync application
argocd app sync color-app --grpc-web

# Get app details
argocd app get color-app --grpc-web

# View app logs
argocd app logs color-app --grpc-web
```

## ArgoCD Integration

This repository is designed to be used with ArgoCD for GitOps deployments. Changes pushed to the master branch will be automatically synchronized by ArgoCD to the target Kubernetes cluster.

### ArgoCD Access
- **UI URL**: http://argocd.local
- **Username**: admin
- **Password**: Check `argo-infra/argocd-access-info.txt`

### ArgoCD Application Configuration
The repository includes an ArgoCD application manifest (`argocd-application.yaml`) with:
- **Auto-sync**: Enabled - automatically applies changes from Git
- **Self-healing**: Enabled - reverts manual changes to match Git
- **Pruning**: Enabled - removes resources deleted from Git
- **Target Branch**: master
- **Namespace**: default

### GitOps Workflow
1. Make changes to Kubernetes manifests in this repository
2. Commit and push to the master branch
3. ArgoCD automatically detects changes and syncs to the cluster
4. Monitor deployment status in ArgoCD UI or CLI

## Related Resources

For the complete tutorial and additional context, refer to:
- Full project documentation: https://github.com/elevy99927/Jenkins-k8s/tree/main/Part4-CICD/04-ArgoCD

## Testing the Application

```bash
# Get service URL (minikube)
minikube service color-service -n default --url

# Check deployment status
kubectl get deployment color-deploy -n default
kubectl get pods -l app=color -n default

# View service endpoints
kubectl get svc color-service -n default
```

## Important Notes

- This repository contains only Kubernetes manifests, not application source code
- The actual container images are hosted on Docker Hub under `elevy99927/color`
- Deployments target nodes with label `node-type: worker`
- Repository URL: https://github.com/renatts/argo-demo-repo.git
- Default branch: master (not main)
