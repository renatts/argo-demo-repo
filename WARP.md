# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Repository Overview

This is a demo repository for ArgoCD tutorials, containing Kubernetes deployment manifests for GitOps workflows. The repository serves as a source of truth for ArgoCD to sync application deployments to a Kubernetes cluster.

## Repository Structure

- **hello-newapp.yaml**: Kubernetes Deployment manifest for a color application
  - Deploys a containerized color service (currently configured for yellow)
  - Uses the image `elevy99927/color:yellow`
  - Configured to run on worker nodes via nodeSelector

## Common Development Tasks

### Updating Application Deployments

To change the deployed application version or configuration:

```bash
# Edit the deployment manifest
vi hello-newapp.yaml

# Commit and push changes for ArgoCD to sync
git add hello-newapp.yaml
git commit -m "Update color deployment"
git push origin main
```

### Changing the Color Service Version

To deploy a different color version, modify the image tag in `hello-newapp.yaml`:
- Line 19: Change `image: elevy99927/color:yellow` to desired color (e.g., `blue`, `green`, `red`)

### Validating Kubernetes Manifests

```bash
# Validate YAML syntax
kubectl apply --dry-run=client -f hello-newapp.yaml

# Check manifest structure
kubectl explain deployment.spec
```

## ArgoCD Integration

This repository is designed to be used with ArgoCD for GitOps deployments. Changes pushed to the main branch will be automatically synchronized by ArgoCD to the target Kubernetes cluster.

### ArgoCD Workflow
1. Make changes to Kubernetes manifests in this repository
2. Commit and push to the main branch
3. ArgoCD detects changes and syncs to the cluster
4. Monitor deployment status in ArgoCD UI

## Related Resources

For the complete tutorial and additional context, refer to:
- Full project documentation: https://github.com/elevy99927/Jenkins-k8s/tree/main/Part4-CICD/04-ArgoCD

## Important Notes

- This repository contains only Kubernetes manifests, not application source code
- The actual container images are hosted on Docker Hub under `elevy99927/color`
- Deployments target nodes with label `node-type: worker`
